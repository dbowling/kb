# Teller

[Teller](https://tlr.dev/) is a command line tool to manage developer secrets.

It works with many sources of secrets, including `.env` files, LastPass, 1Password, and Vault.

## Installation

```shell
brew tap spectralops/tap && brew install teller
```

## Configuration

To get started, we will configure a demo `.env` file to hold our plain text secrets and a `.teller.yml` file to configure teller for this project.

### .env example

Create a `.env` file in your home directory where it is safe from version control and prying eyes.

```shell
cat .env.example > ~/.env.demo 
chmod 0600 ~/.env.demo
cat ~/.env.demo

# Output
DEMO=true
KUBE_CONTEXT=demo
PASSWORD=123456
EMAIL=name@example.com
```

### Configure teller with `.teller.yml`

Write `.teller.yml` in the root of your project.

```yaml
---
project: 2nd Brain

carry_env: false

providers:
  dotenv:
    env:
      API_PASSWORD:
        path: ~/.env.demo
        field: PASSWORD
        serverity: high
        redact_with: "**REDACTED!**"
      KUBE_CONTEXT:
        path: ~/.env.demo
        severity: none
```

## Usage

### Show the managed secrets

```shell
$ teller show

# Output
-*- teller: loaded variables for 2nd Brain using .teller.yml -*-

[dotenv ~/.env.demo] API_PASSWORD = 12*****
[dotenv ~/.env.demo] KUBE_CONTEXT = de*****
```

!!! summary
    Did you notice that Teller only imported the secrets we defined in `.teller.yml`?

    This is a great security feature, letting you explicitly define which secrets are
    allowed to be used in your project.

### Redact secrets from logs

We will simulate an application log tail and Teller will redact the secrets.

```shell
$ (echo 'Simulated log... 123456' && tail .env.example) | teller redact

# Output
Simulated log... **REDACTED!**
DEMO=true
KUBE_CONTEXT=**REDACTED**
PASSWORD=**REDACTED!**
EMAIL=name@example.com
```

Teller redacted the secrets that we defined in `.teller.yml`!

!!! summary
    Even though we defined `EMAIL=name@example.com` in our `.env.demo` file, Teller did not redact it because we did not define it in `.teller.yml`. Use the `env_sync` feature to automatically import all secrets from your `.env` file.

### Load secrets into your shell commands

Simple load, no redaction:

```shell
$ teller run -- echo "PASSWORD: ${API_PASSWORD}"

# Output
-*- teller: loaded variables for 2nd Brain using .teller.yml -*-
PASSWORD: 123456
```

Redact secrets:

```shell
$ teller run --redact -- echo "PASSWORD: ${API_PASSWORD}"

# Output
-*- teller: loaded variables for 2nd Brain using .teller.yml -*-
PASSWORD: **REDACTED!**
```


### Use secrets in your ZSH shell functions

I use `ansible-navigator` to run playbooks against our Red Hat hosts. It requires some specific environment variables to be set, so I load them on-demand with a ZSH function.

```shell
# ~/.zshrc
a () {
	eval "$(teller sh)"
	ansible-navigator "$@"
}

# Usage
a run /path/to/playbook.yml
```

