# Backend Composition And Lifecycle

Use this reference when wiring runtime dependencies, long-lived clients, repositories, outbound clients, services, and application startup or shutdown.

## Composition Root

- Use a composition root for configuration loading, telemetry and logging, client construction, repository and outbound aggregation, service construction, transport construction, lifecycle, and shutdown wiring.
- Make mandatory dependencies explicit in constructors. Make invalid construction fail early or impossible.
- Do not create long-lived concrete clients deep inside handlers or services.
- Construct long-lived clients once at the owning composition seam and pass their contracts explicitly to consumers.
- A composition root spans layers by design; it is gated on length like any other file, not on what it touches.

### Applied

```ts
// Wrong: a new pool per request, config read at the call site, and the
// service cannot be tested without a real database.
class OrderService {
  async place(cmd: PlaceOrder) {
    const db = new Pool({ url: process.env.DATABASE_URL })
    ...
  }
}

// Right: mandatory dependencies are explicit; invalid construction is impossible.
class OrderService {
  constructor(
    private readonly orders: OrderRepository,  // port, not Pool
    private readonly payments: PaymentClient,  // outbound port
    private readonly clock: Clock,             // substitutable
  ) {}
}

// composition root — constructed once, wired explicitly
const config   = loadConfig()                       // fail early if invalid
const pool     = new Pool(config.databaseUrl)
const orders   = new PostgresOrderRepository(pool)  // adapter implements port
const payments = new StripePaymentClient(config.stripeKey)
const service  = new OrderService(orders, payments, systemClock)

onShutdown(() => pool.end())                        // shutdown owned here too
```

The service names `Clock` rather than calling `Date.now()` because time is a real substitution seam. It does **not** inject a pure formatter — that would be ceremony, not a seam.

## Dependency Aggregation

- Use repository or outbound aggregators when a family of dependencies should be grouped, cached, lazily constructed, or passed through request or runtime context.
- Keep aggregators typed and responsibility-focused.
- Do not let an aggregator become an untyped service locator.

## Startup And Shutdown

- Make startup ordering explicit when services depend on initialized clients, storage, migrations, telemetry, or configuration.
- Make shutdown ownership explicit for clients, pools, workers, message consumers, and runtime notifications.
- Close or flush long-lived resources through the composition root and preserve cancellation and failure outcomes.
