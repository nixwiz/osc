# OSC (Operate Sensu Cluster)

A utility intended for operators of multiple Sensu Go clusters. Constantly having to run `sensuctl configure` is tedious. The more clusters, the more headache!

Now you can use OSC to manage your list of Sensu backends, and then switch between them with 1 simple command.

# Usage

Setting up OSC is easy. Create a `osc.config.yaml` file in ~/.config/, and populate it with the following YAML format below.

Multiple profiles can be setup for a single backend, in case you need a different namespace, username or output format. 

OSC could also be used in a CI/CD pipeline to perform actions on multiple clusters. An `osc.config.yaml` can also be read from the current working directory.

# Configuration

```yaml
---
# OSC managed sensuctl config
localhost:
  env: dev
  username: admin
  password: ""
  interactive: true
  api: http://localhost:8080
  trusted-ca-file: "/path/to/trust-ca.pem"
  namespace: default
  format: tabular
  insecure-skip-tls-verify: false
  timeout: 15s

localhost-ssl:
  env: dev
  username: admin
  password: P@ssw0rd!
  api: https://localhost:8080
  namespace: foo
  format: json
  insecure-skip-tls-verify: true

localhost-min:
  username: admin
  password: P@ssw0rd!
  api: https://localhost:8080
```

## Required profile parameters *
A minimum config profile requires 3 settings:

- "api"
- "username"
- "password" *** (If "interactive" is set to true on the profile, this setting is optional) ***

## Optional profile parameters
A profile in the OSC config contains a number of optional parameters. If a parameter is not present, the default value will be used.

- "timeout". (Default: 15s)
- "env" (Default: no value)
- "trusted-ca-file" (Default: no value)
- "namespace" (Default: "default")
- "format" (Default: "tabular")
- "insecure-skip-tls-verify" (Default: false)
- "interactive" (Default: false)

# Commands

Once your configuration file is in place, switching between Sensu backend clusters is a breeze.

`> osc`

```bash
OSC (Operate Sensu Cluster) is a utility to enable Sensu administrators the
ability to quickly switch between clusters using profile configurations.

Usage:
  osc [command]

Available Commands:
  connect     Connect sensuctl to a configured Sensu cluster.
  help        Help about any command
  list        List config profiles, along with active sensuctl settings.

Flags:
  -h, --help   help for osc

Use "osc [command] --help" for more information about a command.
```

## List

List will show you the currentl sensuctl cluster/profile settings, along with the list of available profiles in your OSC config.

`> osc list`

```bash
Active Config
─────────────
⯈ API: http://localhost:8080
⯈ Namespace: default
⯈ Format: tabular

Profile         Environment     Username        Namespace       Format          API
───────         ───────────     ────────        ─────────       ──────          ───
localhost       dev             admin           default         tabular         http://localhost:8080
localhost-ssl   dev             admin           foo             json            https://localhost:8080
```

## Connect

Connect takes 1 argument, the profile `name` in the config. It creates a new sensuctl configuration for you. No need to type in the API, username/password, namespace, format preferences anymore. Woo Hoo!

```bash
osc connect localhost
Connected to Sensu backend: localhost (http://localhost:8080)
```

## Connect (interactive)

If a profile has interactive set to true, OSC will prompt the user for the password associated with that profile username.

```bash
osc connect localhost-auth
? Password for admin: *********
Connected to Sensu backend: localhost-auth (http://localhost:8080)
```
