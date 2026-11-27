# What Exists

Load before adding a function or a type, and when reviewing one that was added. It owns how to find out what a module and a library already hold without reading their files.

## List A Module

Each command prints one line per declaration: where it is, what kind it is, the package or type it belongs to if it belongs to one, its name, and its signature where the tool can give one. Run it on one module's directory, never on the whole repository. Where the project's architecture document has a Function Map section, use the command it gives.

```bash
# Go
ctags -R -x --_xformat='%F:%n  %K  %s %N%S %t' --languages=Go --kinds-Go=fsitacv <directory>
# Rust
ctags -R -x --_xformat='%F:%n  %K  %s %N%S %t' --languages=Rust --kinds-Rust=fPsgitCvM <directory>
# Python
ctags -R -x --_xformat='%F:%n  %K  %s %N%S %t' --languages=Python --kinds-Python=fmcv <directory>
# TypeScript
rg -n '^(export )?(default )?(declare )?(abstract )?(async )?(function|const|let|class|interface|type|enum) \w+' --glob '*.ts' --glob '*.tsx' <directory>
```

```text
session.go:107  func  main projectDirName(cwd string) typename:string
session.go:113  const  main subagentsFolderName- -
all_sessions.go:8  struct  main sessionAgents- -
```

A `-` stands for a missing signature or return type. `ctags` means Universal Ctags. Add `--exclude=<pattern>` for test files and for generated or vendored directories.

## Filter, Do Not Read

A module of a few thousand lines lists hundreds of declarations. Do not read them all. Pipe the command through a search for the noun, the thing your function works on, once for each word the code uses for that thing:

```bash
<command> | rg -i ':[0-9]+[: ].*invoice'
```

The pattern looks only after the line number, so a file or folder named for the noun does not match by its path alone. A noun that is also the package's name still matches every line, so search for the narrower thing.

Search by the noun and not the verb. A planned `loadInvoice` is found as `fetchInvoice` only through `invoice`.

## What The List Leaves Out

- The fields of a Go or Rust struct, the variants of a Rust enum, and the methods a Go interface requires.
- Rust trait implementations: a type's `impl Display` is not listed.
- In TypeScript, the methods of a class, anything declared inside a namespace or exported again through `export { }`, and any part of a signature after its first line.

So the list finds candidates. Before you call one, read its declaration at the line the list gives. Read those few lines, not the file.

## Which Modules To List

Your own module first. Then the modules your change map's Dependency line names. With no map, the modules the architecture document lets your module call. With neither, the modules your module's files already import.

To check one name across the whole repository, run `rg -n -w '<name>'`.

## What A Library Offers

Before writing a helper for something a library the project already uses might do, look at the library. The architecture document's Libraries section, or the project's manifest, says which libraries those are. Run these from the project's root, in the environment the project runs in, and filter a long answer by the noun as above.

```bash
# Go: the package, then one name in it
go doc <package>
go doc <package>.<Name>
# Rust: the public items of a crate, from its source under the cargo registry
rg -n '^\s*pub (async |const |unsafe )*(fn|struct|enum|trait|type|const|static|mod) \w+' --glob '*.rs' ~/.cargo/registry/src/*/<crate>-<version>/src
# Python: the module, then one name in it
python -m pydoc <module>
python -m pydoc <module>.<name>
# TypeScript: the declarations a package ships
rg -n '^\s*(export )?(default )?(declare )?(abstract )?(function|const|let|class|interface|type|enum|namespace) \w+' --glob '*.d.ts' --glob '*.d.mts' --glob '*.d.cts' node_modules/<package>
```

## Without Ctags

Where `ctags` is not installed, say so in your report and list declarations with `rg` instead, searching for the keywords that open one: `func`, `fn`, `def`, `class`, `type`, `struct`, `enum`, `trait`, `interface`.
