---
title: "Running a Local LLM on a Kubernetes Powered Raspberry Pi 5"
description: "How to deploy Ollama on a single-node Raspberry Pi 5 Kubernetes cluster already running a full OpenTelemetry Demo stack, navigating memory constraints, scheduler deadlocks, and model selection on edge hardware."
date: 2026-06-01T18:10:58+10:00
draft: false
image: images/ollama-pi-k8s.svg
enableToc: true
enableTocContent: true
tocPosition: inner
author: amrith
libraries:
- msc
---


# Running a Local LLM on a Kubernetes Powered Raspberry Pi 5

Running an enterprise-grade observability playground *and* a local LLM on a single credit-card-sized computer sounds like an engineering dare. On a Raspberry Pi 5 with 8 GB of RAM and a busy Kubernetes cluster, it is entirely possible, but it requires deliberate choices about memory, scheduling, and model selection.

This post walks through the exact steps taken to deploy Ollama on that cluster, the scheduling traps we hit along the way, and how we got a local AI model responding to prompts without evicting the observability stack.

Don't have Kubernetes on your Pi yet? Follow the [setup guide](/posts/tech/k8s/install-k8s-raspberrypi/) first.

---

## Starting point: a heavily loaded cluster

Before adding anything AI-related, the cluster (`rpi5-debian-k8s`) was already running a full OpenTelemetry Demo deployment in the `default` namespace — Kafka, OpenSearch, Jaeger, Prometheus, Grafana, a frontend proxy, and over a dozen microservices, all coexisting in 8 GB of physical RAM.

```
$ kubectl get pods
NAMESPACE     NAME                                                   READY   STATUS    RESTARTS        AGE
default       my-otel-demo-accountingservice-6c85d9f566-xj74t        1/1     Running   59 (33h ago)    90d
default       my-otel-demo-adservice-76767c5f5c-4r4td                1/1     Running   58 (33h ago)    90d
default       my-otel-demo-cartservice-7846f85f89-mg4vh              1/1     Running   58 (33h ago)    90d
default       my-otel-demo-checkoutservice-5c57b785bb-4rjh4          1/1     Running   58 (33h ago)    90d
default       my-otel-demo-currencyservice-7b7c58bcd7-x7kbg          1/1     Running   58 (33h ago)    90d
default       my-otel-demo-emailservice-8644997cd4-pzqvz             1/1     Running   58 (33h ago)    90d
default       my-otel-demo-flagd-7f89bc5855-qqjwq                    2/2     Running   116 (33h ago)   90d
default       my-otel-demo-frauddetectionservice-6985c6f788-kfwvl    1/1     Running   58 (33h ago)    90d
default       my-otel-demo-frontend-667c747b85-gwg4s                 1/1     Running   58 (33h ago)    90d
default       my-otel-demo-frontendproxy-6b869b5467-r2slz            1/1     Running   58 (33h ago)    90d
default       my-otel-demo-grafana-946d57f48-sr529                   1/1     Running   58 (33h ago)    90d
default       my-otel-demo-imageprovider-7fcdc9494c-sx9fb            1/1     Running   58 (33h ago)    90d
default       my-otel-demo-jaeger-54c79d5d46-tdvfk                   1/1     Running   58 (33h ago)    90d
default       my-otel-demo-kafka-757bc96dcc-bspz7                    1/1     Running   58 (33h ago)    90d
default       my-otel-demo-otelcol-67f77bf8d-wdb98                   1/1     Running   60 (33h ago)    90d
default       my-otel-demo-paymentservice-5c84f75d5f-4762l           1/1     Running   58 (33h ago)    90d
default       my-otel-demo-productcatalogservice-6466c648db-kc6m6    1/1     Running   58 (33h ago)    90d
default       my-otel-demo-prometheus-server-69cb999fb7-nwqnf        1/1     Running   58 (33h ago)    90d
default       my-otel-demo-quoteservice-64c6f45676-p6vnx             1/1     Running   58 (33h ago)    90d
default       my-otel-demo-recommendationservice-788bdd4789-8b42p    1/1     Running   58 (33h ago)    90d
default       my-otel-demo-shippingservice-f75db77cd-wsfbw           1/1     Running   58 (33h ago)    90d
default       my-otel-demo-valkey-6c7c8bcd47-l6v6m                   1/1     Running   58 (33h ago)    90d
default       opensearch-dashboards-7b55bb8df8-n948c                 1/1     Running   117 (33h ago)   279d
default       otel-demo-opensearch-0                                 1/1     Running   117 (33h ago)   279d
kube-system   calico-kube-controllers-d4dc4cc65-zqcvl                1/1     Running   1746 (9h ago)   556d
kube-system   calico-node-llccq                                      1/1     Running   295 (33h ago)   556d
```

With this much already running, resource isolation wasn't optional, it was the only way this would work.

---

## Step 1: Create an isolated namespace

The first step was giving the AI workload its own namespace, keeping it cleanly separated from observability infrastructure:

```bash
$ kubectl create namespace ai
namespace/ai created
```

---

## Step 2: Set up persistent storage with HostPath

Checking for storage classes revealed none were configured:

```bash
$ kubectl get sc
No resources found
```

Without a `StorageClass`, models downloaded into a pod would vanish on restart. The workaround: mount a directory directly from the Pi's filesystem using a `hostPath` volume.

```bash
sudo mkdir -p /mnt/data/ollama
sudo chmod 777 /mnt/data/ollama
```

This gives Ollama a persistent home for model weights across pod restarts.

---

## How the two-phase deployment works

Before getting into the memory constraints, it's worth understanding what the YAML and the `ollama run` command are actually doing, because they're two separate things.

`kubectl apply -f ollama.yaml` deploys the Ollama server: a container running the Ollama runtime, with resource limits, an exposed port, and the HostPath volume mounted at `/root/.ollama`. At this point there is no model. The pod is an empty inference server waiting to be told what to run.

`ollama run qwen2.5:1.5b` is a separate, explicit step you run inside the pod. It tells the Ollama server to pull the `qwen2.5:1.5b` weights from the Ollama model registry and download them into `/root/.ollama` inside the container.

```bash
# Phase 1: deploy the inference server (no model yet)
kubectl apply -f ollama.yaml

# Phase 2: pull the model into the running pod
kubectl exec -it <pod-name> -n ai -- ollama run qwen2.5:1.5b
```

Because `/root/.ollama` inside the container maps to `/mnt/data/ollama` on the Pi's filesystem via the HostPath volume, the downloaded weights land on the Pi's disk. Restart the pod and the model is already there — no 1.5 GB re-download. This is the whole reason Step 2 exists.

---

## Step 3: Navigating the memory constraints

This is where things got interesting. Getting Ollama scheduled and running required working through two distinct failure modes before landing on a configuration that actually held.

### Attempt A: resource oversubscription

The initial plan was to run `phi3:mini` (~2.2 GB) with conservative limits — 2 Gi requested, 3.5 Gi limit. The pod scheduled and the model downloaded successfully, but initialisation failed:

```
Error: 500 Internal Server Error: model requires more system memory (3.5 GiB) than is available (2.0 GiB)
```

Ollama reads the cgroup memory *limit* and uses it as the available system memory figure. With a 3.5 Gi limit and only 2 Gi requested and actually allocated, the engine refused to load the model.

### Attempt B: scheduler deadlock

Raising the request to 3700 Mi and the limit to 4500 Mi resolved the Ollama check — but introduced a different problem. The rolling update tried to schedule the new pod alongside the old one:

```
NAME                      READY   STATUS    RESTARTS   AGE
ollama-584c6d4b6b-g4kt6   1/1     Running   0          11m
ollama-6ffcdfdbf6-mzdq9   0/1     Pending   0          21s
```

With the OpenTelemetry suite consuming most available RAM, the node didn't have another 3700 Mi free to schedule the incoming pod. The old pod wouldn't evict itself; the new pod couldn't schedule. Classic deadlock.

### The solution: lean sizing and a forced eviction

The fix was two-pronged: drop to a much smaller model (`qwen2.5:1.5b` at 1.5 GB) with proportionally smaller resource requests, then manually force-delete the stuck old pod to instantly free physical memory for the incoming replacement.

```bash
$ kubectl apply -f ollama.yaml
$ kubectl delete pod ollama-584c6d4b6b-g4kt6 -n ai --grace-period=0 --force
```

The `--grace-period=0 --force` combination skips the graceful shutdown window and immediately releases the node memory the old pod was holding. The new pod transitioned to `Running` within seconds.

The final `ollama.yaml` that made it work:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ollama
  namespace: ai
  labels:
    app: ollama
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ollama
  template:
    metadata:
      labels:
        app: ollama
    spec:
      containers:
      - name: ollama
        image: ollama/ollama:latest
        resources:
          requests:
            memory: "1500Mi"
            cpu: "1.5"
          limits:
            memory: "3000Mi"
            cpu: "3"
        ports:
        - containerPort: 11434
          name: ollama-port
        volumeMounts:
        - name: ollama-storage
          mountPath: /root/.ollama
      volumes:
      - name: ollama-storage
        hostPath:
          path: /mnt/data/ollama
          type: DirectoryOrCreate
---
apiVersion: v1
kind: Service
metadata:
  name: ollama-service
  namespace: ai
spec:
  type: ClusterIP
  selector:
    app: ollama
  ports:
  - protocol: TCP
    port: 11434
    targetPort: 11434
```

Key sizing rationale:

- **1500 Mi request** — gives the scheduler enough headroom to place the pod without conflicting with the OTel stack
- **3000 Mi limit** — satisfies Ollama's internal memory check for a 1.5 GB model with buffer
- **`qwen2.5:1.5b`** — a 1.5B parameter model, highly optimised for constrained hardware and remarkably capable for its footprint

---

## Step 4: Pull the model and run it

With the pod running, exec directly into it to pull and initialise the model:

```bash
$ kubectl exec -it ollama-7c5d94c75b-gwf2x -n ai -- ollama run qwen2.5:1.5b
pulling manifest
pulling 183715c43589: 100% 1.5 GB
verifying sha256 digest
writing manifest
success
>>> Send a message (/? for help)
```

A quick smoke test:

```
>>> What are 3 fun facts about the Raspberry Pi?

1. **Developed in the UK**: The Raspberry Pi was developed at the University of Cambridge's
   Computer Laboratory. It's now manufactured by the Raspberry Pi Foundation, a UK-based
   charity with a mission to make computing accessible to everyone.

2. **Cost-effective educational tool**: Originally designed to help students learn programming,
   it has since become a cornerstone of hobbyist and professional projects — from home
   automation to custom media servers.

3. **Innovative applications**: The Camera Module turns the Pi into an inexpensive machine
   vision platform. It's also widely used in robotics, environmental monitoring, and edge
   computing research.
```

Well, it works, isn't it? But this is command line, a practical use case is coming soon.

---

## How it works


```msc
User->Terminal: kubectl exec -it ollama-pod -n ai
Terminal->KubernetesAPI: Authenticate and route to pod
KubernetesAPI->OllamaPod: Open exec session (ai namespace)
OllamaPod->User: >>> Send a message (/? for help)

User->OllamaPod: "What are 3 fun facts about the Raspberry Pi?"
OllamaPod->Qwen25Model: Tokenise and run inference
Qwen25Model->Qwen25Model: Load weights from /mnt/data/ollama
Qwen25Model-->OllamaPod: Generated tokens (streaming)
OllamaPod-->User: Response streamed to terminal

Note right of Qwen25Model: Runs entirely on-device\nNo cloud. No data leaving the Pi.
```


## What we actually built

The cluster now runs two distinct workloads side by side:

- **`default` namespace**: full OpenTelemetry Demo stack (Kafka, OpenSearch, Jaeger, Prometheus, Grafana, 18+ microservices)
- **`ai` namespace**: Ollama serving `qwen2.5:1.5b`, with model weights persisted to a local HostPath volume

Total physical RAM in use across both stacks sits comfortably within the 8 GB limit — lean model selection was the key decision that made this possible.

The practical takeaways if you're attempting something similar:

- Ollama uses the cgroup **limit** (not the request) as its available memory figure — size your limit to at least 2× the model's disk footprint
- Rolling updates deadlock when the node lacks RAM for two pods to coexist; force-delete the old pod immediately after applying the new spec
- On constrained hardware, model selection matters more than anything else — `qwen2.5:1.5b` punches well above its weight for general-purpose tasks

The Pi 5 handles both workloads without complaint. Private, local AI inference running alongside a production-grade observability stack, on hardware that fits in your pocket.

---

## Going further: other models and resource constraints

The Ollama server is model-agnostic. When you run `ollama run <model>` inside the pod, Ollama checks whether that model's weights already exist in `/root/.ollama` (backed by `/mnt/data/ollama` on disk). If they do, it loads from disk. If they don't, it pulls from the Ollama registry first. This means the same running pod can serve different models across different sessions — you're not locked to one model at deploy time.

The constraint is RAM. With the OTel stack consuming roughly 4.5 GB, you have around 3.5 GB left for inference. That rules out anything in the 7B+ range.

| Model | Params | Disk size | Min RAM | Runs alongside OTel? |
|---|---|---|---|---|
| `llama3.2:1b` | 1B | ~1.3 GB | ~2 GB | Yes |
| `qwen2.5:1.5b` | 1.5B | ~1.0 GB | ~2 GB | Yes |
| `qwen2.5:3b` | 3B | ~1.9 GB | ~3 GB | Marginal |
| `llama3.2:3b` | 3B | ~2.0 GB | ~3 GB | Marginal |
| `phi3:mini` | 3.8B | ~2.2 GB | ~3.5 GB | Marginal |
| `mistral:7b` | 7B | ~4.1 GB | ~6 GB | No |
| `llama3:8b` | 8B | ~4.7 GB | ~7 GB | No |

```bash
# works — 1.5B, fits alongside OTel stack
ollama run llama3.2:1b

# works — 1.5B, fits alongside OTel stack
ollama run qwen2.5:1.5b

# marginal — monitor memory closely, may evict OTel pods under load
ollama run qwen2.5:3b

# marginal — monitor memory closely, may evict OTel pods under load
ollama run llama3.2:3b

# marginal — sits right at the free RAM ceiling, proceed carefully
ollama run phi3:mini

# will likely OOM — insufficient free RAM with OTel running
ollama run mistral:7b

# will likely OOM — insufficient free RAM with OTel running
ollama run llama3:8b
```

If you want to push into larger models without gutting the OTel stack, the [Raspberry Pi AI HAT+](https://www.raspberrypi.com/products/ai-hat/) is worth considering. It connects via the Pi 5's M.2 connector and adds a 26 TOPS NPU dedicated to ML inference. Offloading inference to the NPU would free the ARM cores and, more importantly, keep model weights off the main RAM — making 7B models more viable on the same board without sacrificing the observability stack.