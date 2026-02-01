# Configuration options

Configuration options are special values provided by the compiler which can be used to:
- configures what code is included when building a certain configuration, via:
  - [`when` item] or any of its variants
  - [conditional compilation attributes]
- be used in expression via their [meta-variable] variants

The possible configuration options are generated per-project, but will always include a fixed set of builtin configurarion values.
Additinally the available values may be extended via [compilation set extensions].

## Attribute predicates [↵](#configuration-options)

[Conditional compilation attributes] allow the configuration to be defined in multiple different ways, any mix of these can be used:
- using helpers to control the predicate:
  - `not`: requires the supplied condition to not be `true`, i.e. `!cond`
  - `all`: requires all supplied condition to be `true`, i.e. `a && b && ...`
  - `any`: requires any supplied condition to be `true`, i.e. `a || b || ...`
- using an expression returning a `bool` to check for certain conditions, i.e. `a == b && c == d`

Checking for features may be done in 2 ways:
- using their name directly, or
- by checking if they have the value of `.available`, i.e. `feature_name == .available`, or include any of their own sub-features, i.e. `feature_name == .subfeature`

> _Note_ Additional ways of using features may be added in the future

## Builtin configuration values [↵](#configuration-options)

A fixed set of configuration values will always be provided, regardless of build configuration.

> _Note_: This section contains an incomplete set of values of which not all variants are supported yet.

> _Note_: In the future, all tables will be generated from compiler data, so to be consistent with supported targets

### `target_arch` [↵](#builtin-configuration-values-)

The `target_arch` value defines the current target architecture.
This value often lines up with the first value in a platform's target triplet.

architecture  | Description
--------------|------------------------------------------------
`interp`      | interpreter
`x86`         | any x86 architecture
`i686`        | 32-bit x86, also known as i386, i686, or IA-32
`x64`         | 64-bit x86, also known as x86_64 or AMD64
`arm`         | any ARM architecture
`arm_thumb`   | any ARM architecture with Thumb support
`aarch32`     | 32-bit ARMv8 and above & Thumb-2
`aarch64`     | 64-bit ARMv8 and above
`armv7`       | 32-bit ARMv7
`riscv64`     | 64-bit RISC-V

> _Note_: Additional architectures will be supported in the future

### `target_feature` [↵](#builtin-configuration-values-)

The `target_feature` value defines the features of the current target architecture.
When multiple architectures are provided, the `target_feature`s must be specified for the specific architectures.

> _Note_: Later on, these sub-sections will be generated from compiler provided data

> _Todo_: Support for versioned features

> _Todo_: Move entire sub-sections into their own file, once generation from compiler data is figures out

#### x86/64 (x86_64) [↵](#target_feature-)

Features specific to any `x86` architecture.
When used as a sub value when multiple architectures are present, it is used as `target_feature(x86 = ...)`.

The compiler assumes that by default, the minimum supported cpu adheres to `x86-64-v1`.
For any older configuration, with the exceptions of certain features such as `.sse`, similar functionality can be provided via a compiler extension.

Features that are not specified below may be accessed via [cpuid].

All currently supported [x86/64 features] can be found on their respective page.

In addition, the following feature groups are available

Group | Features
------|---------------------------------------------------------------------------
`v1`  | `mmx`, `fxsr`, `sse`, `sse2`
`v2`  | `cmpxchg16`, `lahf_sahf`, `popcnt`, `sse3`, `sse4_1`, `sse4_2`, `ssse3`
`v3`  | `avx`, `avx2`, `bmi1`, `bmi2`, `f16c`, `fma`, `lzcnt`, `movbe`, `osxsave`
`v4`  | `avx512f`, `avx512bw`, `avx512cd`, `avx512dq`, `avx512vl`

## ARM [↵](#target_feature-)

Features specific to AArch32/AArch64 architectures.
When used as4 a sub value when multiple architectures are present, it is used as `target_feature(arm = ...)`.

All currently supported [arm features] can be found on their respective page.

In addition, the following feature groups are available

Group  | Features
-------|--------------------------------------
`v8_1` | `crc32`, `lor`, `lse`, `pan`,
`v8_2` | `dpb`, `pan2`, `ras`
`v8_3` | `rcpc`, `pauth_*`
`v8_4` | `dit`, `flagm`, `rcpc2`
`v8_5` | `bti`, `sbe`
`v8_6` | `i8mm`
`v8_8` | `hbc`
`v9_0` | `v8_5`
`v9_6` | `cmpbr`

## RISC-V [↵](#target_feature-)

Features specifc to RISC-V architectures.
When used as a sub value when multiple architectures are present, it is used as `target_feature(riscv = ...)`.

All currently supported [risc-v features] can be found on their respective pave.

In addition, the following feature groups are available

Group    | Features
---------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
`g`      | `m`, `a`, `f`, `d`, `zicsr`, `zifencei`
`sha`    | `h`, `ssstateen`, `shcounterenw`, `shvstvala`, `schtvala`, `shvsatpa`, `shgatpa`
`rva20u` | `m`, `a`, `f`, `d`, `c`, `zicsr`, `zicntr`, `ziccif`, `ziccrse`, `ziccamoa`, `za128rs`, `zicclsm`
`rva20s` | `rva20u`, `zifencei`, `svbare`, `sv39`, `svade`, `ssccptr`, `sstvecd`, `sstvala`
`rva22u` | `m`, `a`, `f`, `d`, `c`, `zicsr`, `zicntr`, `zihpm`, `ziccif`, `ziccrse`, `ziccamoa`, `zicclsm`, `za64rs`, `zihintpause`, `zba`, `zbb`, `, zbs`, `zic64`, `zicbom`, `zicbop`, `zicboz`, `zfhmin`, `zkt`
`rva22s` | `rva22u`, `zifencei`, `svbare`, `sv39`, `svade`, `ssccptr`, `sstvecd`, `sstvala`, `sscounterenw`, `svpbmt`, `svinval`
`rva23u` | `rva22u`, `b`, `v`, `zvfhmin`, `zvbb`, `zvkt`, `zihintntl`, `zicond`, `zimop`, `zcmap`, `zcb`, `zfa`, `Supm`
`rva23s` | `rva23u`, `zifencei`, `svbare`, `sv39`, `svade`, `ssccptr`, `sstvecd`, `sstvala`, `sscounterenw`, `svnapot`, `sstc`, `sscofpmf`, `ssnpm`, `ssu64xl`, `sha`
`rvb23u` | `m`, `a`, `f`, `d`, `c`, `b`, `zicsr`, `zicntr`, `zihpm`, `ziccif`, `ziccrse`, `zicclsm`, `za64rs`, `zihintpause`, `zic64b`, `zicbom`, `zixbop`, `zicbop`, `zicboz`, `zkt`, `v`, `zvfhmin`, `zvbb`, `zvkt`, `zihintntl`, `zicond`, `zimop`, `zcmop`, `zcb`, `zfa`, `zawrs`, `supm`
`rvb23u` | `rvb23u`, `zifencei`, `svbare`, `ssccptr`, `sstvecd`, `sstvala`, `svpbmt`, `svinval`, `svnapot`, `sstc`, `sscofpmf` `ssnpmu`, `ssu64xl`, `sha`

## `target_os` [↵](#builtin-configuration-values-)

The `target_os` value defines the OS the current target.
This value often lines up with the third value in a platform's target triplet.

- `window`
- `linux`
- `macos`
- `android`
- `ios`
- `freebsd`
- `openbsd`
- `netbsd`
- `none`

Each target OS can have their own sub-versioning:

## `target_family` [↵](#builtin-configuration-values-)

The `target_family` value defines which 'family' the current target.
A `family` often represents a set of related OS or architectures.

family    | description
----------|------------------------------------------
`windows` | any NT-kernel OS
`unix`    | any unix-compatible OS (posix compliant)
`wasm`    | any WASM based environment

> _Note_: Certain families may be combined, e.g. `unix` + `wasm`

## `target_env` [↵](#builtin-configuration-values-)

The `target_env` value defines additional disambiguating info about the current environment of the current target.
For example, it can represent which `libc` variant is being used.

By default, this value is set to `null`, unless it is explicitly required to avoid any ambiguity.

env      | description
---------|--------------------------------------
`null`   | no explicit libc
`gnu`    | GNU libc
`msvc`   | msvc crt
`musl`   | musl libc
`sgx`    | Intel Software Guard eXtensions libc
`sim`    | ios simulator
`macabi` | macos libc
`none`   | explicitly not dependents on libc

## `target_abi`  [↵](#builtin-configuration-values-)

The `target_abi` value defines the ABI of the current target.

By default, this value is set to `null`, unless it is explicitly required to avoid any ambiguity.

abi      | description
---------|---------------------------------
`llvm`   | LLVM IR compatible platform abi
`eabihf` | ARM eabi
`abi64`  | x64 abi

## `target_endian`  [↵](#builtin-configuration-values-)

The `target_endian` value defines the endianess of the current target.

- `little`
- `big`
- `mixed`

## `target_pointer_width`  [↵](#builtin-configuration-values-)

The `target_pointer_width` value defines the bit-width of a pointer of the current target.

- `16`
- `32`
- `64`

## `target_vendor`  [↵](#builtin-configuration-values-)

The `target_vendor` value defines the vendor of the current target.

- `apple`
- `fortranix`
- `pc`: any generic PC vendor
- `unknown`

## `target_has_atomic`  [↵](#builtin-configuration-values-)

The `target_has_atomic` value defines the maximum supported size of atomic operations on the current target.

- `8`
- `16`
- `32`
- `64`
- `128`
- `ptr`

## `contracts`  [↵](#builtin-configuration-values-)

The `contracts` value defines which kind of contracts are current enabled

value        | description
-------------|---------------------------------
`none`       | no contracts are enabled
`all`        | all contracts are enabled
`assert`     | assertions are enabled
`pre`        | preconditions are enabled
`post`       | postconditions are enabled
`invar`      | all invariants are enabled
`invar_fn`   | function invariants are enabled
`invar_type` | type invariants are enabled

## `test`  [↵](#builtin-configuration-values-)

The `test` value defined which kind of tests are currently being run

test    | description
--------|--------------------------
`unit`  | unittest are being run
`bench` | benchmarks are being run
`none`  | no tests are being run

## `panic`  [↵](#builtin-configuration-values-)

The `panic` value defines what happens when a panic occurs

- `abort`

> _Note_: Only `abort` is currently supported, as unwinding has not been figured out

## `profile`  [↵](#builtin-configuration-values-)

The `profile` value defines the current profile being compiled.
A profile is a collection of configuration value.

This value can be either of the default profiles, or one defined by the user

default profile | description
----------------|---------------------------------------------------------
`debug`         | full debug information, safety checks, no optimizations
`hardened`      | fully optimized, but retaining all safety checks
`release`       | fully optimized



[conditional compilation attributes]: ./attributes/conditional-compilation.md
[meta-variable]:                      ./metaprogramming.md#meta-variables-
[`when` item]:                        ./items/when-items.md
[x86/64 features]:                    ./configuration-options/x86-64-features.md
[arm features]:                       ./configuration-options/arm-features.md
[risc-v features]:                    ./configuration-options/riscv-features.md
[compilation set extensions]:         #configuration-options "Todo: link to docs"
[cpuid]:                              https://en.wikipedia.org/wiki/CPUID