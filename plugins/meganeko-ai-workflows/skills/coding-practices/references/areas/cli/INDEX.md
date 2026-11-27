# Command Line And Library Area

Use this branch for a command line tool, a script, a hook or filter program that reads standard input, and a library with no service and no UI. Use the [Backend Area](../backend/INDEX.md) instead once the program serves requests or owns a data store.

Apply the guidance by responsibility. A 200 line tool does not get a service's layer tree.

## Shape

- **Entry point.** `main` reads the process: arguments, environment, standard input, the working directory. It turns them into typed values, calls one function with those values, and maps the result to output and an exit code. It holds no rule of its own. It is the only place that reads the environment or exits the process.
- **Run function.** One function takes the typed inputs, and the input and output streams, as parameters, and does the work by calling the others. The whole program is tested through it, with no process spawned.
- **Rules.** Every decision the program makes is a compute function over values: no file, no environment, no clock. Rules that change together share a file named for the rule.
- **Outside world.** Each kind of outside access has one owning file: reading the files the tool works on, reading its own configuration, calling another program or a network service. Those files hold effects and their error mapping, and no rule.
- **Wire formats.** A format the program reads or writes, such as JSON on standard input, a configuration file, or a report, is converted to and from typed values in one file per format. The rest of the program sees typed values only.

## Gates

- A rule never reads a file, the environment, or the clock. The value arrives as a parameter.
- Exit codes, and what goes to standard output and to standard error, are decided in one place, at the entry point.
- A failure the caller must act on reaches the entry point as an error value. Nothing below the entry point exits the process.
- Flag names, configuration keys, and defaults are named constants owned by the file that parses them.
- A library exports the smallest surface its callers use, and nothing in it reads global state: the caller passes in what the library needs.

## Layout

For a small tool, one package or module with one file per responsibility above, named for the responsibility (`limits`, `glob`, `target`), is enough. Add a directory only when a second group of files forms that changes together. Match the repository when it already chose a layout.

## Tests

Test rules directly, values in and values out. Test the whole program through the run function, with in-memory streams and a temporary directory. Process level tests are for the entry point's wiring only: argument parsing and exit codes.

Combine this branch with the active language branch.
