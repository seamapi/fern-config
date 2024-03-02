# Seam API Fern Configuration

Tagging a release on this repository will update the:
- [Java SDK repo](https://github.com/seamapi/java)
- [Go SDK repo](https://github.com/seamapi/go)

## What is in this repository?

This repository contains

- Seam's OpenAPI definition, which lives in the [openapi](./fern/api/openapi/) folder
- Definition (see [.definition](./fern/.definition))
- Generators (see [generators.yml](./fern/api/generators.yml))

To make sure that the OpenAPI is valid, you can use the Fern CLI.

```bash
npm install -g fern-api # Installs CLI
fern check # Checks if the definition is valid
```

### What are generators?

Generators read in your Fern definition files and output artifacts (e.g. the TypeScript SDK Generator) and are tracked in [generators.yml](./fern/api/generators.yml).

To trigger the generators run:

```bash
# output generated files locally
fern generate

# publish generated files
fern generate --group publish --version <version>
```

The publish command currently runs in a GitHub workflow (see [ci.yml](.github/workflows/ci.yml#L32))

## How to re-generate Fern SDKs

### One-Time Set Up
1. Clone the `fern-config` repository to your local machine from here: https://github.com/seamapi/fern-config
1. Install the Fern CLI by running `npm install -g fern-api` in your terminal.

### For Each Re-Generation
#### Import latest OpenAPI.json from Seam Connect
1. For every re-generation, start by creating a new branch in the `fern-config` repository.
1. Obtain the OpenAPI spec for Seam Connect that is currently deployed in production from https://connect.getseam.com/openapi.json to ensure the SDK matches the deployed version.
1. Replace the entire content of `fern-config/fern/openapi.json` with the content from the link above.
1. After saving, compare the changes in `openapi.json` with what was previously committed. Look specifically for changes in the `securitySchemes` element.
1. Revert any changes made to the `securitySchemes` element to preserve manual updates previously made. If additional security schemes have been added, and reverting changes is not sufficient, consult with the Fern team on the `#fern-seam` Slack channel.

#### Generating and Committing Changes
1. Run `fern write-definition` at the top level of your local `fern-config` repository.
1. Commit the changes to the `fern-config` repository, including updates to `openapi.json` and any generated files. Push your branch to GitHub, open, and merge your branch into main. If checks fail, seek advice on `#fern-seam` Slack channel.

#### Releasing
1. After your PR is merged, go to the fern-config releases page on GitHub at https://github.com/seamapi/fern-config/releases.
1. Draft a new release, choose a tag (`go@` or `java@` followed by the next version number, e.g., `go@0.2.9`), and create the new tag.
1. Generate release notes, set as the latest release, and publish the release.
1. Wait for Fern to open a new PR in the [`seamapi/go`](https://github.com/seamapi/go) or [`seamapi/java`](https://github.com/seamapi/java) repository. If this doesn't happen, check the last commit's action logs on main for errors and ask on `#fern-seam` for help.
1. Once the PR is opened, ensure all checks pass before merging it into main.
1. Go to the releases page for the seamapi repository (e.g., https://github.com/seamapi/go/releases), draft a new release following similar steps as before, using the repository's versioning format for the new tag (e.g., `v0.4.9`).
1. Generate release notes, set as the latest release, and publish.

This completes the setup and regeneration process!

## Troubleshooting
### Handling Missing OpenAPI parameters
Fern expects OpenAPI specifications to follow a specific format, which may differ from Seam's configuration. If you find that SDKs lack certain parameters, consult the `fern/openapi/overrides.yml` file. This file allows you to customize or override our OpenAPI specification to ensure completeness.
