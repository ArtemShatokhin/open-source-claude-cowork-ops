# Open-Source Secrets and Connectors in Kortix

[Kortix](https://kortix.com) is the open-source AI Management System, and it
treats a secret and a connector credential as two different things. A secret
is a named encrypted value stored on the project and granted to an agent. A
connector credential is a third-party authorization that Kortix brokers
server-side, so the raw key never enters the machine an agent runs on.

## Secret names live in the repo, values do not

The manifest lists secret names only. Values are encrypted in the platform,
injected into a session at boot, and never written to the repo or the logs:

```yaml
env:
  required: [STRIPE_API_KEY]
  optional: [LINEAR_API_KEY]
```

One agent sees a secret only when its `secrets:` list names it. A value can be
shared with a person, a group or an agent, or restricted to the person who set
it. When an agent is not listed, it receives no project secrets, so the first
grant switches a project into a governed state instead of leaving a blanket
default. By default a granted value arrives as an environment variable in that
session. A stricter egress-enforced mode, where the sandbox holds a handle and
Kortix substitutes the real value outside it, is experimental as of October
2026.

## Connector credentials stay server-side

Connect a tool once for the whole company. Kortix stores the connection, not
the password, and agents reach it through one scoped project token. That token
is the only credential a sandbox holds. Every outbound call is assembled on
the server side: the gateway checks that the agent may use the connector,
decrypts the credential, attaches it to one request and throws it away. The
model is never shown a credential, and the ledger keeps a hash of the inputs
instead of the inputs themselves.

Turning a connector off takes effect on the next call, and nothing in the
sandbox needs rotating because nothing in the sandbox was ever your key. A
connector belongs to one project, so another project cannot see it or read its
credential.

## Connector reach is granted per agent

An agent gets the connectors its manifest lists and nothing else, and
effective access is the intersection of what the person can do and what the
agent was granted. A
connector can be owned by the project, where everyone shares one
authorization, or by each member, where a person acts as themselves and an
automated principal cannot act at all.

Kortix connects 3,000+ apps in a click (checked October 2026). It also points
at an OpenAPI or Postman spec, a GraphQL endpoint, a remote MCP server, or a
bare HTTP base URL, and turns every operation into a tool.

| Source | What you provide |
| --- | --- |
| 3,000+ apps | The app, then its OAuth screen |
| MCP | A remote server URL |
| OpenAPI / Postman | A spec |
| GraphQL | An endpoint |
| HTTP | A base URL |

See [Read the docs](https://kortix.com/docs) for the full command and config
reference; the source is [Kortix on GitHub](https://github.com/kortix-ai/suna).
