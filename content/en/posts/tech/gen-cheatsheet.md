---
title: "Cheat sheets"
description: "Frequently used commands and cheatsheets for quick reference"
date: 2010-12-28T13:37:05+11:00
draft: false
author: Amrith
authorEmoji: 👨‍💻
tags:
- tech
- cheatsheet
categories:
- cheatsheet
hideToc: false
enableToc: true
enableTocContent: false
image: images/icons/gen-cheatsheet-icon.png
---

This is a personal cheatsheet for quick reference
#### Vagrant
{{< expand "vagrant commands" >}}

{{< highlight tf >}}
# Initialise the vagrant file
vagrant init

# Start a VM
vagrant up

# SSH to the VM
vagrant SSH

# Stop a Vagrant VM
vagrant halt

# Reload the config and restart the VM
vagrant reload

# Resume a vagrant VM
vagrant resume
{{< /highlight >}}

{{< /expand >}}


#### Docker
{{< expand "docker commands" >}}
{{< highlight shell >}}
# Run(exec) a command in running container(<id>) interactively(i) with pseudo-TTY(t) at the given shell
sudo docker exec -it <id> /bin/bash

{{< /highlight >}}
{{< /expand >}}


#### Gcloud

{{< expand "gcloud commands" >}}
{{< highlight tf >}}

# Authorize gcloud to access the Cloud Platform with Google user credentials

gcloud auth login
# Acquire new user credentials to use for Application Default Credentials
gcloud auth application-default login
{{< /highlight >}}

{{< /expand >}}
#### Hugo commands

{{< expand "hugo commands" >}}

{{< highlight hugo >}}
# Run hugo locally. Draft posts are not seen
hugo server

# Run hugo locally. Draft posts are visible
hugo -D Server

# Build the website. All files are now placed in the public folder
hugo

# Deploy the public files to target. Target details need to be populated in the config file
hugo deploy --target=gcs

{{< /highlight >}}
{{< /expand >}}


#### Dynatrace USQL

{{< expand "Dynatrace User Session Query commands" >}}
{{< highlight sql >}}

# Count the number of usersession where application has a specific useraction

select count(*) as " " from usersession where useraction.application="<application-name>" AND useraction.name="loading of page /cannotreachhost.html"

{{< /highlight >}}
{{< /expand >}}




#### Dynatrace OneAgentCtl Commands

{{< expand "Dynatrace OneAgentCtl Commands" >}}
{{< highlight tf >}}

# Oneagentctl Path

# Linux or AIX:
<INSTALL_PATH>/agent/tools, by default /opt/dynatrace/oneagent/agent/tools
You need root privileges.
# Docker-based deployment
<INSTALL_PATH>/agent/tools, by default /opt/dynatrace/oneagent/agent/tools
Note that this path will differ for a volume-based deployment.
# Windows:
<INSTALL_PATH>\agent\tools, by default %PROGRAMFILES%\dynatrace\oneagent\agent\tools
You need administrator privileges. If you try to run oneagentctl in a non-admin Windows console, Windows will display a User Account Control pop-up and fail.
# Example:
Linux or AIX:
./oneagentctl --set-proxy=my-proxy.com --restart-service
Windows:
.\oneagentctl.exe --set-proxy=my-proxy.com --restart-service


# Options:
(WARNING: Avoid copy-pasting the commands and use two hypens without spaces - - )

NOTE: Use two hyphens without spaces ./oneagentctl - -get-proxy
  -h [ --help ]                       # Shows this help message.
  -v [ --version ]                    # Shows OneAgent version.
  --get-proxy                         # Shows current proxy address.
  --set-proxy arg                     # Sets proxy address to <arg>.
  --get-tenant                        # Shows current environment ID.
  --set-tenant arg                    # Sets environment ID to <arg>. Always use in combination with --set-tenant-token.
  --get-tenant-token                  # Shows current tenant token.
  --set-tenant-token arg              # Sets tenant token to <arg>.
  --get-server                        # Shows current server address.
  --set-server arg                    # Sets server address to <arg>.
  --get-host-group                    # (Early adopter) Shows current host group assignment.
  --set-host-group arg                # (Early adopter) Sets host group to <arg>. The <arg> canonly contain alphanumeric characters, hyphen (-), underscore (_), and dot (.), it can not start with 'dt.' and must be shorter than 100 characters.
  --get-host-id                       # Shows host identifier.
  --get-host-id-source                # Shows host identifier calculation source.
  --set-host-id-source arg            # Sets host identifier calculation source.
  --set-host-property arg             # Sets host property in <key> or <key>=<value> format. Both <key> and <value> must not contain equals sign (=) or whitespace characters, <key> cannot start with hash symbol (#), and combined must be shorter than 256 characters.
  --remove-host-property arg           # Removes host property.
  --get-host-properties                # Shows all host properties.
  --set-host-tag arg                   # Sets host tag in <key> or <key>=<value> format. Both <key> and <value> must not contain equals sign (=) or whitespace characters, <key> cannot start with hash symbol (#), and combined must be shorter than 256 characters.
  --remove-host-tag arg                # Removes host tag.
  --get-host-tags                      # Shows all host tags.
  --get-host-name                      # Shows host name.
  --set-host-name arg                  # Sets host name to <arg>. The <arg> must not contain angle brackets (<>), ampersand (&), single or double quotes (', ") or newline (CR, LF) characters, it cannot start with hash symbol (#), and it must be shorter than 256 characters.
  --get-watchdog-portrange             # Shows current watchdog port range.
  --set-watchdog-portrange arg         # Sets the watchdog port range to <arg>. The <arg> must contain two port numbers separated by a colon (:). For example 50000:50100. The maximum supported port range is from 1024 to 65535 and must cover at least 4 ports. The port number starting the range must be lower.
  --get-system-logs-access-enabled     # Shows if access to system log files for proactive support is enabled.
  --set-system-logs-access-enabled arg # Controls if access to system log files for proactive support is enabled.
  --get-infra-only                     # Shows if Infrastructure-Only Monitoring mode is enabled.
  --set-infra-only arg                 # Controls if Infrastructure-Only Monitoring mode is enabled.
  --get-network-zone                   # Shows current network zone assignment.
  --set-network-zone arg               # Sets network zone to <arg>. The <arg> can only contain alphanumeric characters, hyphen (-), underscore (_), and dot (.), it must be shorter than 256 characters, and must not start with dot (.).
  --restart-service                    # Restarts OneAgent service. Can be combined with setters to execute stop->apply->start sequence automatically.
  --get-auto-injection-enabled         # Shows if process auto-injection is enabled.
  --set-auto-injection-enabled arg     # Controls if process auto-injection is enabled. Use with caution as this setting cannot be changed remotely via cluster-side config.
  --get-auto-update-enabled            # Shows if auto-updates are enabled.
  --set-auto-update-enabled arg        # Controls if auto-updates are enabled. Use with caution as this setting cannot be changed remotely via cluster-side config.
  --create-support-archive arg         # Creates a support archive. Use directory=<path> (default: current working directory) to specify the output directory, days=<int> (default: 7) to specify the timeframe.
  --get-app-log-content-access         # Shows if access to log files for the purpose of log monitoring is enabled.
  --set-app-log-content-access arg     # Controls if access to log files for the purpose of log monitoring is enabled.
  --get-extensions-ingest-port         # Shows current extensions ingest HTTP port.
  --set-extensions-ingest-port arg     # Sets extensions ingest HTTP port.
  --get-extensions-statsd-port         # Shows current extensions statsd port.
  --set-extensions-statsd-port arg     # Sets extensions statsd port.

# Examples:

$sudo ./oneagentctl --get-server

{*https://sg-ap-southeast-2-5-187-6-prod40-sydney.live.dynatrace.com/communication;https://sg-ap-southeast-2-3-15-33-prod40-sydney.live.dynatrace.com/communication;https://sg-ap-southeast-2-3-231-36-21-prod40-sydney.live.dynatrace.com/communication;https://sg-ap-southeast-2-3-10-35-71-prod40-sydney.live.dynatrace.com/communication}{https://tenantid.live.dynatrace.com:443/communication}

$ sudo ./oneagentctl --get-host-id
6b899713d5b4a22c

$ sudo ./oneagentctl --get-host-id-source
auto

$ sudo ./oneagentctl --get-host-group

$ sudo ./oneagentctl --get-host-id
6b899713d5b4a22c

$ sudo ./oneagentctl --get-host-id-source
auto

$ sudo ./oneagentctl --get-host-properties

$ sudo ./oneagentctl --get-host-tags
app=easytravel
env=test
mgmt-zone=test
os=linux

$ sudo ./oneagentctl --get-watchdog-portrange
50000:50100

$ sudo ./oneagentctl --get-system-logs-access-enabled
true

$ sudo ./oneagentctl --get-infra-only
false

$ sudo ./oneagentctl --get-network-zone
default

$ sudo ./oneagentctl --get-auto-update-enabled
true

$ sudo

$ sudo ./oneagentctl --get-extensions-ingest-port
14499

$ sudo ./oneagentctl --get-extensions-statsd-port
18125



Parameters starting with "--set" require OneAgent service to be stopped first.
For details, see https://www.dynatrace.com/support/help/shortlink/oneagentctl
# Oneagentctl options

{{< /highlight >}}
{{< /expand >}}
