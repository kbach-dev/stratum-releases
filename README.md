# stratum — releases

Prebuilt binaries for **stratum**, a tiny declarative IaC tool written in Rust.
Scoped to system bootstrap: provision a DigitalOcean droplet, then install
packages, drop config files, manage systemd services, configure the firewall,
and run containers on it — one `.strat` config, one `apply`.

## Install

```sh
# Linux / macOS
curl --proto '=https' --tlsv1.2 -LsSf https://github.com/sotheara-say/stratum-releases/releases/latest/download/stratum-cli-installer.sh | sh
```

```powershell
# Windows
powershell -ExecutionPolicy Bypass -c "irm https://github.com/sotheara-say/stratum-releases/releases/latest/download/stratum-cli-installer.ps1 | iex"
```

## Quick taste

```hcl
secret "do_token" {
  from_env = "DO_API_TOKEN"
}

resource "do_droplet" "web" {
  token    = secret.do_token.value
  region   = "sgp1"
  size     = "s-1vcpu-1gb"
  image    = "ubuntu-24-04-x64"
  ssh_keys = ["my-key"]
}

host "web" {
  from_resource = "do_droplet.web"
}

resource "docker_container" "hello" {
  host  = host.web.addr
  name  = "hello"
  image = "traefik/whoami"
  ports = ["80:80"]
}
```

```sh
stratum plan        # preview: 1 create + deferred bootstrap
stratum apply -y    # droplet -> bootstrapped box -> running container
stratum destroy -y --allow-destroy   # tear it all down again
```

Secrets flow to providers but never into state — only redacted markers with
sha256 hashes are recorded. State is a local JSON file next to your config.

## Docs

The full book (language reference, providers, tutorials) is published at
<https://sotheara-say.github.io/stratum-releases/>.

## Issues

Bug reports and feature requests welcome right here in this repo's issues.