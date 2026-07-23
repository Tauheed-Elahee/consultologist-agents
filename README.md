# consultologist-agents

Agent manifests for [Consultologist](https://app.consultologist.ai) — the
canonical, git-controlled definition of every Foundry agent and the
output-contract catalog. Seeded from the app repo (Consultologist-Blazor)
on 2026-07-23; design in its `docs/customizable-workflow/content-repos.md`.

## Full GitOps

Merging to `main` IS the publish event:

- A changed `agents/{name}.yaml` → CI creates the new **Foundry agent
  version** (`POST …/agents/{name}/versions`) and asserts the returned
  version number equals the yaml's `version:`, then mirrors the
  **redacted** manifest to the registry
  (`agent-definitions/{name}/{version}/definition.yaml`).
- A changed `agents/output-contracts.json` → CI publishes the catalog
  version (schemas first, catalog last, immutable versions).

Publishing ≠ activating: the app's `AzureAI__*Version` pins gate which
version runs. The app's startup attestation compares the deployed Foundry
agent against the registry mirror — since only this repo's CI can write
either, git is the single channel and drift is detectable by construction.

CI authenticates via GitHub→Azure OIDC (no stored secrets); human registry
writes are retired.
