# Rust Values And Boundaries

Use when Rust code receives external data, defines a domain value, or handles fallible behavior.

## Newtypes

- Use newtypes for IDs and domain-safe values.
- Prefer validated constructors for values created from untrusted input.
- Avoid unchecked conversion helpers that hide validation.

```rust
// Wrong: interchangeable, and nothing stops the arguments being swapped.
fn transfer(from: Uuid, to: Uuid, amount: i64)

// Right: the compiler rejects a swap, and the unit is explicit.
fn transfer(from: AccountId, to: AccountId, amount: Cents)
```

## Boundary Parsing

- Keep parsing and validation at boundaries with `TryFrom`, `FromStr`, serde validation, or explicit constructors.
- Use the project error type for fallible behavior.

```rust
impl TryFrom<String> for EmailAddress {
    type Error = InvalidEmail;
    fn try_from(raw: String) -> Result<Self, Self::Error> { /* validate once */ }
}
```

A `pub fn new_unchecked` that skips validation must be justified by an upstream guarantee, named to say so, and kept in the module owning the invariant. It is never a convenience for callers that find the checked path inconvenient.

## Tests

- Prefer hand-written fakes in tests. Use mock crates sparingly when a fake would be noisy.
