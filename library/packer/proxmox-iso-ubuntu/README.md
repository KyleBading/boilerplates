The right pattern for a DevOps/homelab boilerplate repo — using Ansible (or similar tools like Python/Jinja2) to render Packer templates from structured YAML defaults and .j2 sources before building images.

In IaC workflows, Packer templates are kept as Jinja2 (.j2) files for flexibility, with YAML holding schema/defaults/variable hierarchies, and a render step (Ansible template module, Makefile, or CI/CD job) generates the plain .pkr.hcl files Packer consumes directly.

This keeps:

    YAML: human-readable structure, validation schemas, and defaults (like template.yaml).

    Jinja2: the templating logic for conditional Packer HCL (like proxmox-iso-ubuntu.pkr.hcl.j2).

    Rendered HCL: what Packer actually builds with, versioned separately if needed.

Ansible is a top choice because it is already used it for provisioning, and Packer has a native ansible provisioner for post-build config — making this a natural pre-build step.

Build structure matches established boilerplates:

    Pre-render: Ansible/Make renders .j2 + YAML → .pkr.hcl (the playbook does this).

    Multi-env: template.yaml defaults + overrides-dev.yml → lab builds vs. prod.

    CI/CD: GitHub Actions/Jenkins runs the render + packer build on push.

    Secrets: Runtime values like API tokens stay in .pkrvars.hcl or Vault, not baked into defaults.


The playbook follows Ansible best practices: local-only execution (hosts: localhost), vars_files for data, set_fact for transformation, and template for rendering.
Next-level improvements

    Add overrides.yml for lab/prod variants: packer_vars: "{{ overrides | default({}) | combine(template_vars) }}".

    Makefile wrapper: make render runs the playbook, make build runs Packer.

    GitHub Actions for automated builds on tag pushes.


What render-packer.yml does
The playbook loads template.yaml, pulls the default value from each spec.*.vars.* entry, flattens those into a single variable map, and uses that map to render proxmox-iso-ubuntu.pkr.hcl.j2 into a real proxmox-iso-ubuntu.pkr.hcl plus http/user-data.j2 into http/user-data.

It also copies variables.pkrvars.hcl.example to variables.pkrvars.hcl if that file does not already exist, so you have a place to put the Proxmox API secret and any runtime overrides before running Packer.

Why?
The Jinja Packer template is not referencing nested YAML paths like spec.general.vars.image_name.default; it is referencing flat names like {{ image_name }} and {{ disk_storage }}, so a straight vars_files: template.yaml alone would not be enough without transforming the YAML shape first.
This playbook handles that mismatch by converting the structured YAML schema into the flat variable names your .j2 files actually expect.

How to use it
Place the playbook in the same directory as your Packer boilerplate files, then run ansible-playbook render-packer.yml to generate the real files Ansible will render for Packer and cloud-init.

After that, review variables.pkrvars.hcl, set your real proxmox_api_token_secret, and run packer init . && packer build -var-file=variables.pkrvars.hcl proxmox-iso-ubuntu.pkr.hcl.

One caveat
Your variables.pkrvars.hcl.example currently only covers the Proxmox API variables, while many of the build settings are coming from defaults in template.yaml, so if you want environment-specific builds later, the clean next step is to add an overrides file or move more runtime values into variables.pkrvars.hcl.
