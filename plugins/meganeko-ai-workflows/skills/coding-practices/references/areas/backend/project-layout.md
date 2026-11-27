# Backend Project Layout

Load when starting a new backend, adding a top-level folder or a context, or deciding which folder a new file goes in. It owns how a backend's responsibilities become folders, in any language. An existing repository that already chose a layout keeps it.

## Layers At The Top, Contexts Inside

The top-level code folders are the layers. Inside a layer, each context gets one folder. A context is a part of the product with its own rules.

| Folder | Holds | May import |
| --- | --- | --- |
| `server/<transport>` | one folder per way a use case is triggered (an API, a queue consumer, a scheduled job): routes, controllers, request and response shapes, error to protocol mapping | `services`, `pkg` |
| `services/<context>` | the context's types, rules, use cases, and errors | another context under `services`, `pkg` |
| `repositories/<context>` | queries, and the mapping between stored rows and the service's types | `services`, `infrastructure`, `pkg` |
| `outbound/<system>` | clients for systems the project does not own | `services`, `infrastructure`, `pkg` |
| `infrastructure/<concern>` | configuration, connections, technology with no product knowledge | `pkg` |
| `pkg/<concern>` | code with no product knowledge that several layers use, such as logging | nothing in the project |

The entry point sits where the language starts a program, outside the layers. It imports every layer: it builds everything once and starts a transport. A service imports no other layer but `pkg`: it declares what it needs, and the entry point hands it the implementation. The reasons are in [Architecture And Dependencies](./architecture-and-dependencies.md), the wiring in [Composition And Lifecycle](./composition-and-lifecycle.md).

A reader finds a behavior by its layer and its context: the answer is the path.

A new context is an architecture decision: ask. Once decided, it is a folder of that name in each layer that needs one. A folder that holds no product code, such as migrations or end to end tests, sits beside the layers. Any other new top-level folder is a decision too.

## The Language's Habit Does Not Replace It

Where a language community's habitual project skeleton differs from this layout, this layout wins.

- A folder the language needs stays, such as the source root a build expects or the package that names the imports, and the layers sit directly inside it.
- A folder that is only habit is not created: a wrapper that hides the layers, a catch-all source folder the build does not need, or a folder whose only job is to stop other projects importing code that nothing imports (`internal/` in Go is one).

## What Not To Create

- A folder per domain that holds every layer.
- A shared folder for types, such as `models`, `dto`, or `entities`. A product type lives in its context under `services`; a request or response shape lives with its transport.
