# Python Language

Use this branch for Python services, scripts, libraries, and tests: boundaries, dependency wiring, error types, and domain-sensitive values.

- Keep framework objects at transport boundaries.
- Use typed request/domain models where the project supports them.
- Validate untrusted input before domain logic.
- Keep side-effecting clients behind small adapters.
- Prefer explicit dependency injection over hidden global service lookup.
- Keep tests focused on behavior and boundary contracts.
- Do not over-abstract just because static languages need explicit seams.
- Use simple functions/classes for simple deterministic logic.
- Use protocols, abstract base classes, or constructor injection for durable boundaries: databases, external APIs, queues, clocks/time, object storage, authorization clients, and license clients.
- Keep each domain's exception types with that domain, and format responses in one place per protocol boundary.
- Do not declare a Protocol or abstract base class that has one implementation and no test fake.
- Avoid patching too deep into implementation details when a small fake or explicit dependency would be clearer.
- Use value objects, dataclasses, validation models, or `typing.NewType` for domain-sensitive primitives when they improve clarity or validation.
- Keep schema and model definitions in their own file once code outside the package imports them. A small package keeps them beside the behavior.
