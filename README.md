# SBOM products: Eclipse Che

This repository lists the SBOMs of [Eclipse Che](https://eclipse.dev/che/) to analyze with
[ORT Server](https://eclipse-apoapsis.github.io/ort-server/) and the
[ORT SBOM plugin](https://heurtematte.github.io/ort-sbom-plugin/). It contains no source code: at each run, ORT Server
retrieves the SBOMs from the Eclipse Foundation SBOM registry, [sbom.eclipse.org](https://sbom.eclipse.org), and
analyzes their components like the dependencies of source code: vulnerabilities, licenses, rules, and reports.

| File               | Purpose                                                                     |
| ------------------ | --------------------------------------------------------------------------- |
| `sbom-sources.yml` | The SBOMs to analyze: the Dependency-Track project and releases of Che.     |
| `.ort.yml`         | Enables only the `SBOM` package manager for this repository.                |

`che-server` is the only part of Eclipse Che published on sbom.eclipse.org.

## Setup in ORT Server

ORT Server must include the ORT SBOM plugin, for instance with the
[images with the plugin](https://heurtematte.github.io/ort-sbom-plugin/ort-server/).

1. In the organization, create two secrets: one with any value as user name, for instance `sbom-eclipse-org-user`,
   and one with an API key of sbom.eclipse.org that has the `VIEW_PORTFOLIO` permission, for instance
   `sbom-eclipse-org-api-key`.
2. In the organization, create an infrastructure service with the URL `https://sbom.eclipse.org` and these secrets as
   user name and password. ORT Server passes them to the plugin for all requests to sbom.eclipse.org.
3. Add this repository to a product, as a Git repository.
4. Start a run on the `main` branch. `.ort.yml` already enables the `SBOM` package manager. Enable the advisors, such as
   OSV, and the evaluator and reporter as needed. The scanner is not useful here, as it would download the source code
   of all packages.

The run contains one project per source, such as `SBOM:org.eclipse.che:che-server:7.124.0`, with about 280 packages
each, and no issues. If the API key is missing or refused, the run reports an error for each source instead.

## Analyzing a new release

Change the `version` of the sources in `sbom-sources.yml` and commit. Each commit records which releases are analyzed,
so the results of a run can always be traced back to the SBOMs it analyzed.

The plugin also supports `version: latest`, which selects the version that Dependency-Track flags as the latest
version of a project. On sbom.eclipse.org, no version of `che-server` is flagged, so this repository pins the
releases.
