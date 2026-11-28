# Coder Card

Load before any code change and keep it in view. Load it again after a context summary. Each line is a rule. The file named at its end owns the detail and the examples: open it only when the line is unclear. Where the two differ, that file wins.

## Before You Write

1. Read your brief, and the architecture document's lines for the modules you touch. Read the files you need several at a time, in one message. Read a file again only after a context summary, or when something other than your own edit may have changed it: another agent, a formatter, a generator.
2. List what your module and the modules it may call already hold, with the list command the architecture document gives, filtered by the noun. Use what is there. [What Exists](./what-exists.md)
3. For a standard job, such as a timeout, routing, a retry, or parsing, use the library facility your brief's Reuse line names. Where it names none and a library the project uses does the job, or most of it, wrap the library and add the missing part. [Function Shape](./function-shape.md)
4. Stop and ask before adding a module, a dependency between modules, a library, a stored field your brief does not name, or your own version of a job a project library already does, and before deciding a behavior your brief leaves open. [Core Practices](./core.md)

## While You Write

5. Build in this order. First your brief's function plan, exactly as given: the top function with its calls, and a typed placeholder for each other function, until it builds. With no function plan, write the top function yourself, as a sequence of calls, and take the placeholders from it. Then the tests, each seen failing. Then one function at a time, in the plan's order: fill it, build, and run the tests. A test may still fail only at a placeholder not yet filled; fix any other failure before you start the next. A planned function that cannot work as written goes back to the lead unchanged. [Function Shape](./function-shape.md)
6. One job per function. Say what the body does in one sentence: if it needs "and", split it. A product rule never shares a function with a database, network, file, or clock call. [Function Shape](./function-shape.md)
7. The name is the explanation: the domain's words, a verb and its result for a function, the unit on a quantity, the truth about what it writes or sends. Never `handle`, `process`, `data`, `result`, or `tmp`. [Readability](./readability.md)
8. Name every limit, timeout, size, and protocol value, beside the rule that owns it. [Readability](./readability.md)
9. Return early. Two levels of nesting at most. No boolean parameter that switches behavior. More than three parameters become one named structure. [Readability](./readability.md)
10. Write no comments. Only a directive a tool acts on, and a note on a measured speed optimization, stay. [Readability](./readability.md)
11. An error propagates, or is handled with a stated reason and a log. No silent fallback. [Core Practices](./core.md)
12. A clock, a database, a network client, or a random source arrives as a parameter. [Core Practices](./core.md)
13. Never start a thread, a task, or a goroutine only to bound a call's time: pass the deadline to the call. Concurrent work you start or edit survives the cases your area branch lists; for a backend that is [What Breaks](./areas/backend/what-breaks.md).
14. Export only what another module uses. No `utils`, `helpers`, or `common`. [Core Practices](./core.md)

## Tests

15. Test each changed responsibility through the seam your brief names. [Core Practices](./core.md)
16. One behavior area per pair of files, `<area>_positive` and `<area>_negative`. Code that only tests call lives in the test files. [Core Practices](./core.md)
17. Write each test before its code and see it fail; a test that passes before its code exists does not test it. A test gives the same result on every run: where it waits for something to happen, it waits for the event, under a limit that fails the test, never for a fixed time; and it releases what it opens on every exit. A fix starts with a test failing for the reported case. Never break finished code to watch a test fail. [Core Practices](./core.md)
18. Cases differing only in input and expected result are rows of one test, positive and negative rows in their own files. [Core Practices](./core.md)
19. Cover every failure case that applies to what you built or edited, asked or not, each with a test. Your area branch lists them; for a backend that is [What Breaks](./areas/backend/what-breaks.md).

## Before You Report

20. Build, lint, and tests pass, and no placeholder is left.
21. Search your diff: each value joined by hand into text another parser reads goes through that format's builder or escaper; each discarded result and empty handler follows rule 11. Then read the diff once against rules 6 to 14, and look for one rule written in two places. Fix what you find.
22. Report the functions and types you added and the ones you reused, each with its file, the test for each failure case, and anything you could not verify.
