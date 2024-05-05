# ADR 003: LDAP Organizational Data

2021-11-17

The Software Catalog supports organizational data - users and groups - in order to assign ownership of software.

We have numerous sources of organizational data, including:

- Okta
- AWS Simple AD
- google sheets
- `system.internal.com`
  - graphiql endpoint
  - data topics

Some conceptual ways the Software Catalog can be populated with organizational data are:

- direct ingestion from an LDAP compatible service
- direct ingestion using a custom processor
- yaml files, similar to how we use a `catalog-info.yaml` to import other entities

## Decision

We will directly ingest organizational data from the [Okta LDAP integration].

## Status

Superseded - ADR 005

## Consequences

The Okta LDAP service returns all users and groups.
LDAP queries are necessary to filter only the organizational data applicable to the dev-portal.
LDAP query manipulation might be an ongoing maintenance as we discover excluded entities that should be included or anytime reorgs occur.

Running the dev-portal locally requires using a staging environment of Okta in order to bypass MFA requirements.
In production, [redacted].

### Paths Not Taken

Google sheets🤦🏽‍♀️

AWS Simple AD, the org has been moving away from this.

`system.internal.com`

- the owners were not comfortable with the dev-portal relying upon the graphiql endpoint
- subscribing to the data topics would require custom code and additional complexity without creating differentiated value

[okta ldap integration]: https://help.okta.com/en/prod/Content/Topics/Directory/ldap-agent-main.htm
