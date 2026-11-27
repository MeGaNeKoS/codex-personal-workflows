# What Breaks

Load when a change adds or changes a component of a kind below, and when writing its brief or reviewing it. Each line is a case the component survives.

Whoever writes the brief turns every line that applies into a requirement with its test. Whoever builds covers every line that applies, asked or not, and names the test for each in the report. A line that does not apply is named as such, with the reason.

## A Server That Accepts Requests

- A handler crashes: that request gets an error response, the cause is logged, and the server keeps serving.
- A client sends its headers or body slowly, or never finishes: the connection is cut after a bounded time.
- A request is larger than allowed, by its declared length or as it streams: it is refused before it is read whole.
- A handler runs past the time limit: the caller gets a timeout response, and the next request on that connection is served.
- The process is told to stop: requests in flight finish within a bounded time, and what is left is cut.
- Input the caller controls reaches a response, a log, or a metric label: its length and characters are bounded first.
- Many requests arrive at once: shared state is safe, shown by the language's race checker where it has one.

## A Client Of An Outside System

- The system is slow or never answers: every call has a time limit.
- The system fails once and then works: a retry is bounded, backs off, and is used only for a call that is safe to repeat.
- The system keeps failing: the caller gets a typed error, and nothing is left half written.
- The system answers with something unexpected: the answer is decoded and checked, not trusted.

## A Store

- Two writers race for the same record or slot: one wins, by a constraint or a transaction, never by a check in code alone.
- A write that spans several records fails midway: nothing of it remains.
- A record written by an older version is read: it still decodes.

## A Background Job

- The process stops mid-run: the work is picked up again, and doing it twice is harmless.
- One item keeps failing: it is set aside after a bounded number of tries, and the rest go on.
- Nothing is due: the job waits without spinning.

## A Command

- Required input is missing or invalid: it exits nonzero and names what is wrong.
- It is interrupted: it leaves nothing half done.
