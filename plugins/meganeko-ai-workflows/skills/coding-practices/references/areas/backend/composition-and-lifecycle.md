# Backend Composition And Lifecycle

Use this reference when wiring runtime dependencies, long-lived clients, repositories, outbound clients, services, and application startup or shutdown.

## Composition Root

- Use a composition root for configuration loading, telemetry and logging, client construction, repository and outbound aggregation, service construction, transport construction, lifecycle, and shutdown wiring.
- Make mandatory dependencies explicit in constructors. Make invalid construction fail early or impossible.
- Do not create long-lived concrete clients deep inside handlers or services.
- Construct long-lived clients once at the owning composition seam and pass their contracts explicitly to consumers.

## Dependency Aggregation

- Use repository or outbound aggregators when a family of dependencies should be grouped, cached, lazily constructed, or passed through request or runtime context.
- Keep aggregators typed and responsibility-focused.
- Do not let an aggregator become an untyped service locator.

## Startup And Shutdown

- Make startup ordering explicit when services depend on initialized clients, storage, migrations, telemetry, or configuration.
- Make shutdown ownership explicit for clients, pools, workers, message consumers, and runtime notifications.
- Close or flush long-lived resources through the composition root and preserve cancellation and failure outcomes.
