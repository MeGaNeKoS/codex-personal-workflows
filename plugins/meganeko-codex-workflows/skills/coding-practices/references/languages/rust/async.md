# Rust Async

- Prefer Tokio primitives already used by the project.
- Keep cancellation and shutdown paths explicit for long-running tasks.
- Avoid holding locks across `.await`.
- Use bounded channels for backpressure-sensitive paths.
- Name task ownership clearly; do not hide critical runtime tasks in incidental helpers.
- Surface timeout, retry, and backoff rules as named configuration or constants.
