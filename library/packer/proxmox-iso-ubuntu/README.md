Yes, this is exactly the right pattern for a DevOps/homelab boilerplate repo like yours — using Ansible (or similar tools like Python/Jinja2) to render Packer templates from structured YAML defaults and .j2 sources before building images.
Why it's standard practice

In IaC workflows, Packer templates are kept as Jinja2 (.j2) files for flexibility, with YAML holding schema/defaults/variable hierarchies, and a render step (Ansible template module, Makefile, or CI/CD job) generates the plain .pkr.hcl files Packer consumes directly.

This keeps:

    YAML: human-readable structure, validation schemas, and defaults (like your template.yaml).

    Jinja2: the templating logic for conditional Packer HCL (like your proxmox-iso-ubuntu.pkr.hcl.j2).

    Rendered HCL: what Packer actually builds with, versioned separately if needed.

Ansible is a top choice because your homelab already uses it for provisioning, and Packer has a native ansible provisioner for post-build config — making this a natural pre-build step.
Common repo patterns

Your structure matches established boilerplates:

    Pre-render: Ansible/Make renders .j2 + YAML → .pkr.hcl (your playbook does this).

    Multi-env: template.yaml defaults + overrides-dev.yml → lab builds vs. prod.

    CI/CD: GitHub Actions/Jenkins runs the render + packer build on push.

    Secrets: Runtime values like API tokens stay in .pkrvars.hcl or Vault, not baked into defaults.

Validation from similar repos

Proxmox/Packer homelab repos often use this exact flow for Ubuntu templates with cloud-init, exactly like your ISO + autoinstall setup.

The playbook I gave you follows Ansible best practices: local-only execution (hosts: localhost), vars_files for data, set_fact for transformation, and template for rendering.
Next-level improvements

    Add overrides.yml for lab/prod variants: packer_vars: "{{ overrides | default({}) | combine(template_vars) }}".

    Makefile wrapper: make render runs the playbook, make build runs Packer.

    GitHub Actions for automated builds on tag pushes.
