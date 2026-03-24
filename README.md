# Kyle's `Boilerplates`

## What are Boilerplates?

**Boilerplates** is a collection of production-ready templates for your homelab and infrastructure projects. Stop copying configurations from random GitHub repos or starting from scratch every time you spin up a new service!

## Boilerplates CLI

The Boilerplates CLI tool gives you instant access to battle-tested templates for Docker, Terraform, Ansible, Kubernetes, and more.

Each template includes sensible defaults, best practices, and common configuration patterns—so you can focus on customizing for your environment.

**Key Features:**
- **Quick Setup** - Generate complete project structures in seconds
- **Fully Customizable** - Interactive prompts or non-interactive mode with variable overrides
- **Smart Defaults** - Save your preferred values and reuse across projects

### Requirements

- **Python 3.10 or higher** is required
- Git
- pipx (automatically installed by the installer script)

> **Note for RHEL/AlmaLinux/Rocky Linux 9 users:** These distributions ship with Python 3.9 by default. You need to install Python 3.11 or later:
> ```bash
> sudo dnf install python3.11
> pipx reinstall --python python3.11 boilerplates
> ```

### Installation

#### Automated installer script

Install the Boilerplates CLI using the automated installer:

```bash
# Install latest version
curl -fsSL https://raw.githubusercontent.com/christianlempa/boilerplates/main/scripts/install.sh | bash

# Install specific version
curl -fsSL https://raw.githubusercontent.com/christianlempa/boilerplates/main/scripts/install.sh | bash -s -- --version v1.2.3
```

The installer uses `pipx` to create an isolated environment for the CLI tool. Once installed, the `boilerplates` command will be available in your terminal.

#### Nixos

If you are using nix flakes

```bash
# Run without installing
nix run github:christianlempa/boilerplates -- --help

# Install to your profile
nix profile install github:christianlempa/boilerplates

# Or directly in your flake
{
  inputs.boilerplates.url = "github:christianlempa/boilerplates";

  outputs = { self, nixpkgs, boilerplates }: {
    # Use boilerplates.packages.${system}.default
  };
}

# Use in a temporary shell
nix shell github:christianlempa/boilerplates
```

### Quick Start

```bash
# Explore
boilerplates --help

# Update Repository Library
boilerplates repo update

# List all available templates for a docker compose
boilerplates compose list

# Show details about a specific template
boilerplates compose show nginx

# Generate a template (interactive mode)
boilerplates compose generate authentik

# Generate with custom output directory
boilerplates compose generate nginx my-nginx-server

# Non-interactive mode with variable overrides
boilerplates compose generate traefik my-proxy \
  --var service_name=traefik \
  --var traefik_enabled=true \
  --var traefik_host=proxy.example.com \
  --no-interactive
```

### Managing Defaults

Save time by setting default values for variables you use frequently:

```bash
# Set a default value
boilerplates compose defaults set container_timezone="America/New_York"
boilerplates compose defaults set restart_policy="unless-stopped"

```

### Template Libraries

Boilerplates uses git-based libraries to manage templates. You can add custom repositories:

```bash
# List configured libraries
boilerplates repo list

# Update all libraries
boilerplates repo update

# Add a custom library
boilerplates repo add my-templates https://github.com/user/templates \
  --directory library \
  --branch main

# Remove a library
boilerplates repo remove my-templates
```

## Documentation

Originally created by Christian Lempa. His repo was forked into this one.

For comprehensive documentation, advanced usage, and template development guides, visit [Christian Lempa's YouTube Channel](https://www.youtube.com/@christianlempa).

## Contribution

## Other Resources

- [Cheat-Sheets Repo](https://github.com/christianlempa/cheat-sheets) - Command Reference for various tools and technologies

---

## Legacy Templates (v1)

Looking for templates from my older videos? The original boilerplates collection is still available in the [`backup/boilerplates-v1`](https://github.com/ChristianLempa/boilerplates/tree/backup/boilerplates-v1) branch. These templates haven't been migrated to the new CLI tool yet, but you can still access and use them directly from that branch.
