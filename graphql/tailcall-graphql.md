# Tailcall GraphQL API

Tailcall is a high-performance GraphQL gateway and runtime built in Rust. It does not expose a fixed hosted GraphQL API endpoint — instead, it is a self-hosted runtime that developers configure and deploy themselves. The runtime generates a unified GraphQL schema at startup by composing multiple upstream REST, gRPC, and GraphQL APIs using declarative SDL files annotated with custom directives.

The GraphQL endpoint served by a running Tailcall instance is `/graphql` by default (configurable via `@server(routes: { graphQL: "/graphql" })`). Introspection is enabled by default and can be toggled with `@server(introspection: false)`.

**Endpoint:** `http://<host>:<port>/graphql` (default port 8000; self-hosted)
**Status Endpoint:** `http://<host>:<port>/status`
**Documentation:** https://tailcall.run/docs/

## Authentication

Tailcall itself does not mandate a specific auth scheme — authentication is delegated to upstream services. Outbound request headers (including `Authorization`) are forwarded to upstream services via `@http(headers:)`, `@grpc(headers:)`, and `@graphQL(headers:)` directives. The `@upstream(allowedHeaders:)` directive controls which inbound client headers are propagated upstream.

JWT-based authentication can be enabled via `@link(type: Jwks, src: "path/to/jwks.json")` combined with the `@protected` directive on fields or types to restrict access.

## Directive Vocabulary

The Tailcall SDL schema is extended by a set of custom directives that bind GraphQL fields and types to upstream data sources:

- `@server` — Schema-level server configuration (port, introspection, batching, response timeout, CORS headers, etc.)
- `@upstream` — Global upstream connection settings (HTTP caching, connection pool, keep-alive, batch settings, allowed forwarded headers)
- `@http` — Binds a field to a REST API endpoint (url, method, body, headers, query params, batchKey, dedupe, select, onRequest, onResponseBody)
- `@grpc` — Binds a field to a gRPC method (url, method, body, headers, batchKey, dedupe, select, onResponseBody)
- `@graphQL` — Binds a field to an upstream GraphQL field (url, name, args, headers, batch, dedupe)
- `@cache` — Enables field or type-level response caching (maxAge in milliseconds)
- `@link` — Imports external resources: Config, Protobuf, Script, Cert, Key, Operation, Htpasswd, Jwks, Grpc (id, src, type, headers, meta, protoPaths)
- `@const` — Returns a constant value inline for a field (data)
- `@modify` — Renames or omits a field in the generated schema (name, omit)
- `@addField` — Flattens a nested field path to the top level of a type (name, path)
- `@protected` — Marks a field or type as requiring authentication
- `@js` — Binds a field to a JavaScript function (name)
- `@expr` — Binds a field to a static expression or template
- `@call` — Re-uses an existing resolver from a query or mutation field
- `@telemetry` — Enables OpenTelemetry instrumentation at the schema level

**References:**
- Documentation: https://tailcall.run/docs/
- Getting Started: https://tailcall.run/docs/getting_started/
- Directives Reference: https://tailcall.run/docs/guides/operators/
- CLI Reference: https://tailcall.run/docs/guides/cli/
- GitHub Repository: https://github.com/tailcallhq/tailcall
- N+1 Batching Guide: https://tailcall.run/docs/guides/n+1
- Deployment (AWS Lambda): https://github.com/tailcallhq/tailcall-on-aws
- NPM Package: https://www.npmjs.com/package/@tailcallhq/tailcall
