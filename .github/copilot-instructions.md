# Copilot Instructions for awesome-selfhosted-data

This repository contains structured data (YAML files) used to generate the [awesome-selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted) list and the [awesome-selfhosted.net](https://awesome-selfhosted.net) website.

## Repository Structure

```
.
├── software/           # Individual YAML files for each software project
├── tags/              # Category/tag definitions
├── platforms/         # Programming language/platform definitions
├── licenses.yml       # Open-source license definitions
├── licenses-nonfree.yml # Non-free license definitions
├── markdown/          # Template files for markdown export
├── .hecat/            # Configuration for hecat (build tool)
└── .github/           # GitHub workflows and templates
```

## Key Technologies

- **Data Format**: YAML (strict schema required)
- **Build Tool**: [hecat](https://github.com/nodiscc/hecat) - Python-based data processing tool
- **Automation**: GNU Make + GitHub Actions
- **Python**: Version 3.x with venv for isolation

## Software Entry Schema

Each software project is a YAML file in `software/` with this structure:

```yaml
name: "Project Name"                    # REQUIRED: Display name
website_url: "https://example.com"      # REQUIRED: Official homepage
source_code_url: "https://github.com/..." # REQUIRED: Source repository URL
description: "Short description."       # REQUIRED: < 250 chars, sentence case
licenses:                               # REQUIRED: List of SPDX identifiers
  - Apache-2.0
  - MIT
platforms:                              # REQUIRED: Languages/deployment platforms
  - Python
  - Docker
tags:                                   # REQUIRED: Functional categories
  - Automation
  - Monitoring
depends_3rdparty: false                 # OPTIONAL: Default false
demo_url: "https://demo.example.com"    # OPTIONAL: Interactive demo only
related_software_url: "https://..."     # OPTIONAL: Plugins/addons/clients
```

### Metadata Fields (Auto-generated)

These fields are automatically populated by workflows and should NOT be manually edited:

- `stargazers_count`: GitHub stars
- `updated_at`: Last repository update
- `archived`: Repository archive status
- `current_release`: Latest release info
- `commit_history`: Monthly commit counts

## File Naming Conventions

- **Software files**: Use `kebab-case` (e.g., `my-awesome-software.yml`)
- **Tag files**: Use `kebab-case` (e.g., `task-management--to-do-lists.yml`)
- **Platform files**: Use lowercase (e.g., `python.yml`, `nodejs.yml`)

## Common Operations

### Adding New Software

1. Create `software/my-software.yml` following the schema above
2. Ensure all REQUIRED fields are present
3. Remove comments and unused optional fields
4. Verify licenses exist in `licenses.yml`
5. Verify platforms exist in `platforms/`
6. Verify tags exist in `tags/`
7. Run `make awesome_lint` to validate

### Adding a New Tag/Category

1. Create `tags/my-category.yml`:
```yaml
name: "Category Name"
description: "[Category](https://wikipedia.org/...) description with markdown."
related_tags:      # OPTIONAL
  - Other Category
external_links:    # OPTIONAL
  - title: Related Resource
    url: https://...
```

2. Minimum of 3 software projects must reference the tag
3. Use existing `Miscellaneous` tag for uncategorized software

### Adding a License

Add to `licenses.yml` (for open-source) or `licenses-nonfree.yml`:

```yaml
- identifier: SPDX-ID    # Use SPDX identifier when possible
  name: Full License Name
  url: https://link-to-license-text
```

### Adding a Platform

Create `platforms/platform-name.yml`:

```yaml
name: "Platform Name"
description: "[Platform](https://wikipedia.org/...) description with markdown."
```

## Build and Validation Commands

```bash
# Install build tools (creates .venv/)
make install

# Validate YAML data against guidelines
make awesome_lint

# Strict validation (for CI)
make awesome_lint_strict

# Export to markdown (awesome-selfhosted repo)
make export_markdown

# Export to HTML (awesome-selfhosted.net)
make export_html

# Check for dead links
make url_check

# Update project metadata from GitHub API
make update_metadata

# Update AUTHORS file
make authors

# Clean generated files
make clean
```

## GitHub Actions Workflows

- **build.yml**: Exports data to markdown and HTML repositories (production)
- **pull-request.yml**: Validates PRs before merge
- **daily-update-metadata.yml**: Updates project metadata daily
- **check-dead-links.yml**: Monitors for broken URLs
- **check-unmaintained-projects.yml**: Flags stale projects

## Content Guidelines

### Description Best Practices

- Keep under 250 characters
- Use sentence case (not title case)
- Avoid redundant terms: "open-source", "free", "self-hosted" (implied)
- Prefer short forms: "Minimalist game" vs "A minimalist game"
- For non-English docs: Add "(documentation in LANGUAGE)" at end
- For alternatives: Add "(alternative to PRODUCT)" at end
- For forks: Add "(fork of PROJECT)" at end

### Inclusion Criteria

**Include:**
- Self-hosted web applications
- Server-side software with web interfaces
- Actively maintained projects (commits in last 6-12 months)
- Projects released more than 4 months ago
- Software with working installation instructions

**Exclude:**
- Cloud-dependent software
- Desktop/mobile/CLI apps requiring separate sync servers
- Libraries, SDKs, frameworks (no end-user application)
- PaaS/serverless platforms
- Generic container/virtualization tools (→ awesome-sysadmin)
- Simple Dockerizations without added value

## Linting Rules

The `hecat` awesome_lint processor checks for:

- Valid YAML syntax
- Required fields present
- License identifiers exist in licenses.yml
- Platform names exist in platforms/
- Tag names exist in tags/
- Description length < 250 characters
- URLs are accessible (in url_check)
- Projects have releases/tags
- Projects not archived
- Projects updated in last 12 months (with exceptions)

Exceptions for last-update warnings are defined in `.hecat/awesome-lint.yml`.

## Working with hecat

hecat is a Python-based static site generator and data processor. Configuration files in `.hecat/`:

- `import.yml`: Import from markdown awesome-selfhosted
- `export-markdown.yml`: Export to markdown format
- `export-html.yml`: Export to HTML site
- `awesome-lint.yml`: Validation rules (lenient)
- `awesome-lint-strict.yml`: Strict validation for CI
- `url-check.yml`: Dead link detection
- `update-metadata.yml`: GitHub API metadata fetching
- `conf.py`: Sphinx configuration for HTML export

## Important Notes for AI Agents

1. **Never modify auto-generated fields**: `stargazers_count`, `updated_at`, `archived`, `current_release`, `commit_history`
2. **Always run `make awesome_lint` after changes**: Catches schema violations early
3. **Use existing examples**: Check similar software in `software/` before creating new entries
4. **Preserve YAML formatting**: Consistent indentation (2 spaces), no tabs
5. **One change per PR**: Makes review easier (see PULL_REQUEST_TEMPLATE.md)
6. **Check for duplicates**: Search existing software before adding
7. **Verify external resources**: Ensure licenses, tags, and platforms exist before referencing
8. **Respect curation guidelines**: Review CONTRIBUTING.md for removal criteria

## Testing Your Changes

```bash
# Basic validation
make install
make awesome_lint

# Full export test (requires git repos to be cloned)
make export_markdown
make export_html

# Check a specific software file
source .venv/bin/activate
hecat --config .hecat/awesome-lint.yml
```

## Common Pitfalls

1. **Incorrect YAML syntax**: Use 2-space indentation, proper list formatting
2. **Missing required fields**: All software must have name, website_url, source_code_url, description, licenses, platforms, tags
3. **Non-existent references**: Referencing tags/platforms/licenses that don't exist
4. **Description too long**: Keep under 250 characters
5. **Using title case**: Use sentence case for descriptions
6. **Adding build artifacts to git**: The `.venv/`, `awesome-selfhosted/`, `awesome-selfhosted-html/`, `html/` directories are gitignored

## Quick Reference

**Add software**: Create `software/project-name.yml` with all required fields  
**Add tag**: Create `tags/category-name.yml`, ensure ≥3 software use it  
**Add platform**: Create `platforms/platform.yml` with name and description  
**Add license**: Add entry to `licenses.yml` or `licenses-nonfree.yml`  
**Validate**: `make install && make awesome_lint`  
**Remove software**: Delete the YAML file in `software/`  
**Update metadata**: Runs automatically daily via GitHub Actions  

## Support and Questions

- **Issues**: [GitHub Issues](https://github.com/awesome-selfhosted/awesome-selfhosted-data/issues)
- **Contributing Guide**: [CONTRIBUTING.md](../CONTRIBUTING.md)
- **Templates**: `.github/ISSUE_TEMPLATE/` and `.github/PULL_REQUEST_TEMPLATE.md`
- **Main Project**: [awesome-selfhosted/awesome-selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted)
