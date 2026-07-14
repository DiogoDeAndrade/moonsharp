# MoonSharp Unity Package (Spellcaster Studios fork)

This branch (`upm/beta/v3.0_debug`) is the upstream `upm/beta/v3.0` package plus the
fork changes from `master` of https://github.com/DiogoDeAndrade/moonsharp:

* Binary dumps (`Script.Dump` / `Script.LoadStream`) preserve per-instruction source
  line/column info, so errors from precompiled chunks keep real file:line locations.
* A null `Instruction.Name` survives a dump round-trip (no more `near ''` in errors).
* `DUMP_CHUNK_VERSION` bumped to 0x152 — dumps made with older versions are rejected
  ("Invalid version"); regenerate any cached bytecode.

See the fork's root `README.md` for details and for how this branch is regenerated.

Install in Unity `manifest.json`:

```json
"org.moonsharp.moonsharp": "https://github.com/DiogoDeAndrade/moonsharp.git?path=/interpreter#upm/beta/v3.0_debug"
```
