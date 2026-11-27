# Coder Card

Load before any code change and keep it in view. Load it again after a context summary. Each line is a rule. The file named at its end owns the detail and the examples: open it only when the line is unclear. Where the two differ, that file wins.

## Before You Write

1. Read your brief, and the architecture document's lines for the modules you touch.
2. List what your module and the modules it may call already hold, with the list command the architecture document gives, filtered by the noun. Use what is there. [What Exists](./what-exists.md)
3. For a standard job, such as a timeout, routing, a retry, or parsing, use the library facility your brief's Reuse line names. Where it names none and a library the project uses does the job, or most of it, wrap the library and add the missing part. [Function Shape](./function-shape.md)
4. Stop and ask before adding a module, a dependency between modules, a library, a stored field your brief does not name, or your own version of a job a project library already does. [Core Practices](./core.md)

## While You Write

5. Write the top function first, as a sequence of calls. Give each missing function a typed placeholder, build, then fill them one at a time. [Function Shape](./function-shape.md)
6. One job per function. Say what the body does in one sentence: if it needs "and", split it. A product rule never shares a function with a database, network, file, or clock call. [Function Shape](./function-shape.md)
7. The name is the explanation: the domain's words, a verb and its result for a function, the unit on a quantity, the truth about what it writes or sends. Never `handle`, `process`, `data`, `result`, or `tmp`. [Readability](./readability.md)
8. Name every limit, timeout, size, and protocol value, beside the rule that owns it. [Readability](./readability.md)
9. Return early. Two levels of nesting at most. No boolean parameter that switches behavior. More than three parameters become one named structure. [Readability](./readability.md)
10. Write no comments. Only a directive a tool acts on, and a note on a measured speed optimization, stay. [Readability](./readability.md)
11. An error propagates, or is handled with a stated reason and a log. No silent fallback. [Core Practices](./core.md)
12. A clock, a database, a network client, or a random source arrives as a parameter. [Core Practices](./core.md)
13. Export only what another module uses. No `utils`, `helpers`, or `common`. [Core Practices](./core.md)

## Tests

14. Test each changed responsibility through the seam your brief names. [Core Practices](./core.md)
15. One behavior area per pair of files, `<area>_positive` and `<area>_negative`. Code that only tests call lives in the test files. [Core Practices](./core.md)
16. A test fails when the behavior it protects is removed. Name the line whose removal it catches before you call it done. [Core Practices](./core.md)
17. A fix starts with a test that fails for the reported case.
18. Cover every failure case that applies to what you built, asked or not, each with a test. Your area branch lists them; for a backend that is [What Breaks](./areas/backend/what-breaks.md).

## Before You Report

19. Build, lint, and tests pass, and no placeholder is left.
20. Read your diff once against lines 6 to 13, and look for one rule written in two places. Fix what you find.
21. Report the functions and types you added and the ones you reused, each with its file, the test for each failure case, and anything you could not verify.
