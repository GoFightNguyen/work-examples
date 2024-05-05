# Catalog Backend Module for RabbitMQ

This is an extension module to the plugin-catalog-backend plugin,
providing ingestion of RabbitMQ Exchanges from `internal-system-redacted`.

## Business Goals

| Stakeholder               | Goal/Outcome                                                                  | Context                                                                                                                                                               |
| ------------------------- | ----------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maintainer of an Exchange | Exchanges are automatically surfaced in the dev-portal                        | I manually add/remove Exchanges from my catalog-info so the dev-portal Software Catalog is up-to-date. Even harder is remembering to do this.                         |
| Software Developer        | RabbitMQ Exchanges are discoverable through the dev-portal and are up-to-date | When my BC requires consuming data from another BC, I use numerous channels to discover what mediums are available to me. One of these mediums is RabbitMQ Exchanges. |

## Architecturally Significant Requirements

### Constraints

| Constraint                                                           | Type      | Context                                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------------------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ingest RabbitMQ Exchanges into the Software Catalog                  | technical | The dev-portal Software Catalog is for discovering the org's software ecosystem                                                                                                                                                                                                                                                                             |
| Ingest RabbitMQ Exchanges from `internal-system-redacted` production | technical | `internal-system-redacted` is the well-known, authoritative bounded context providing guardrails for interacting with RabbitMQ. It also provides an endpoint for surfacing only those exchanges meeting the org's naming convention.</br></br>Exchanges are created through `internal-system-redacted`; promotion from staging to production is negligible. |

### Risks

| Condition                                                                   | Consequence                                   | Mitigation Approach                                                                                                                                                                                                                                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Exchanges get removed from our source-of-truth (`internal-system-redacted`) | Catalogued exchanges become orphaned entities | Reduce the Probability</br></br>Ingestion through a processor cannot delete entities, only orphan them.<br />Ingestion through a provider can delete entities so we will ingest through a provider.</br></br>[More info on ingestion](https://backstage.io/docs/features/software-catalog/external-integrations)</br>[More info on orphans](https://backstage.io/docs/features/software-catalog/life-of-an-entity#orphaning) |

## Implementation Decisions

The provider is responsible for simple mappings (e.g. `metadata.name` is `exchange`).

The processor is responsible for setting values that require logic (e.g. getting the `owner` from the `system`).
When querying the `catalogApi`, there is a risk the target entity is not yet cataloged;
this might result in non-deterministic behavior.
If this does happen, it _should_ resolve itself in the next cycle.
