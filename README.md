MoonSharp       [![CI](../../actions/workflows/ci.yml/badge.svg)](../../actions/workflows/ci.yml) [![Build Status](https://img.shields.io/nuget/v/MoonSharp.svg)](https://www.nuget.org/packages/MoonSharp/)
=========
http://www.moonsharp.org   

## About this fork

This is a fork of [moonsharp-devs/moonsharp](https://github.com/moonsharp-devs/moonsharp), maintained by Spellcaster Studios for the *Blitz & Massive* remaster. Differences from upstream (all on `master`):

* **Binary dumps keep source locations.** `Script.Dump` now serializes the per-instruction source refs (line/column spans) and `Script.LoadStream` restores them, remapped to the source the dump is loaded into. Upstream discards them, so every error thrown from precompiled bytecode reported line 0; with this fork, error messages and debugging info from dumped chunks match the source-loaded ones exactly. We use this to cache compiled Lua chunks and skip the parser at startup without losing error navigation.
* **`Instruction.Name` round-trips as `null`.** Upstream dumps a null `Name` as `""`, which made errors from dumped chunks grow a bogus `near ''` fragment.
* `DUMP_CHUNK_VERSION` was bumped `0x151` → `0x152`; dumps produced before/after this change are mutually incompatible (loaders throw "Invalid version" — just regenerate any cached bytecode).

### Unity package branch

The Unity project consumes the `upm/beta/v3.0_debug` branch:

```json
"org.moonsharp.moonsharp": "https://github.com/DiogoDeAndrade/moonsharp.git?path=/interpreter#upm/beta/v3.0_debug"
```

Upstream generates `upm/*` branches via CI: pushing a `vX.Y.Z-<prerelease>` tag runs `.github/workflows/upm-release.yml`, which takes the `unity-package-tgz` artifact from the `ci.yml` run for that commit and republishes it onto orphan branches `upm/<channel>/vX` and `upm/<channel>/vX.Y` (channel = prerelease prefix, e.g. `beta`). Those branches are *generated output*, not source — don't develop on them.

`upm/beta/v3.0_debug` is maintained manually instead of via CI: it was branched from the generated `upm/beta/v3.0` and the files that changed on `master` since the `v3.0.0-beta.1` tag were copied from `src/MoonSharp.Interpreter/` into `interpreter/Runtime/` (the layout matches 1:1; `interpreter/Runtime/MoonSharp.Interpreter.asmdef` replaces the `.csproj`, and `.meta` files only need regenerating when files are added — see `tools/upm/upm-common.sh`). To update it after new `master` changes, repeat that copy and bump `interpreter/package.json`.




A complete Lua solution written entirely in C# for the .NET, Mono, Xamarin and Unity3D platforms.

Features:
* 99% compatible with Lua 5.2 (with the only unsupported feature being weak tables support)
* Support for metalua style anonymous functions (lambda-style)
* Easy to use API
* **Debugger** support via Debug Adapter Protocol e.g. Visual Studio Code
* Runs on .NET 4.5, .NET Platform (formerly Core), Mono, Xamarin and Unity
* Runs on Ahead-of-time platforms like iOS
* Runs on IL2CPP converted code
* No external dependencies, implemented in as few targets as possible
* Easy and performant interop with CLR objects, with runtime code generation where supported
* Interop with methods, extension methods, overloads, fields, properties and indexers supported
* Support for the complete Lua standard library with very few exceptions (mostly located on the 'debug' module) and a few extensions (in the string library, mostly)
* Async method support
* Supports dumping/loading bytecode for obfuscation and quicker parsing at runtime
* An embedded JSON parser (with no dependencies) to convert between JSON and Lua tables
* Easy opt-out of Lua standard library modules to sandbox what scripts can access
* Easy to use error handling (script errors are exceptions)
* Support for coroutines, including invocation of coroutines as C# iterators 
* REPL interpreter, plus facilities to easily implement your own REPL in few lines of code
* Complete XML help, and walkthroughs on http://www.moonsharp.org

For highlights on differences between MoonSharp and standard Lua, see http://www.moonsharp.org/moonluadifferences.html

Please see http://www.moonsharp.org for downloads, infos, tutorials, etc.

## Unity Package (UPM)

### Build package locally

```bash
tools/upm/stage-local-package.sh 3.0.0-local
cd .upm-staging/org.moonsharp.moonsharp
npm pack
```

This produces a tarball like:

`org.moonsharp.moonsharp-3.0.0-local.tgz`

### Install in Unity

Install from version branch:

1. In your Unity project's `Packages/manifest.json`, add:
   `"org.moonsharp.moonsharp": "https://github.com/moonsharp-devs/moonsharp.git?path=/interpreter#upm/v3.0"`
2. If you just want to pin to a major version (3 instead 3.0), use branches like:
   `upm/v3`
3. The VSCode debugger is a separate package and can be added with:
   `"org.moonsharp.debugger.vscode": "https://github.com/moonsharp-devs/moonsharp.git?path=/debugger/vscode#upm/v3.0"`

<blockquote>
<p>[!NOTE]
Beta branches are available with names like `upm/beta/v3.0`
</p></blockquote>

**License**

The program and libraries are released under a 3-clause BSD license - see the license section.

Parts of the string library are based on the KopiLua project (https://github.com/NLua/KopiLua).
Debugger icons are from the Eclipse project (https://www.eclipse.org/).


**Usage**

Use of the library is easy as:

```C#
double MoonSharpFactorial()
{
	string script = @"    
		-- defines a factorial function
		function fact (n)
			if (n == 0) then
				return 1
			else
				return n*fact(n - 1)
			end
		end

	return fact(5)";

	DynValue res = Script.RunString(script);
	return res.Number;
}
```

For more in-depth tutorials, samples, etc. please refer to http://www.moonsharp.org/getting_started.html
