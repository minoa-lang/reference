# Configuration options

Configuration options can be used inside [conditional compilation attributes] and the [`when` expressions](./expressions/when-expressions.md).

The possible configuration options are generated per-project and may be extended past the built-in values by compilation set extensions (_TODO: link to compiler docs_).

> _Note_: This section contains the currently supported and planned values, some may not be supported yet

## `target_arch` [↵](#configuration-options-)

This value defines which architecture the code is being compiled for.

Architecture | Description
-------------|-------------
`.interp`    | interpreter
`.x64`       | x86_64
`.aarch64`   | 64-bit arm (currently unsupported)
`.riscv`     | riscv (currently unsupported)

## `target_feature` [↵](#configuration-options-)

Defines the features available on the current architecture.
If a feature for a differen architecture is used then is allowed in the current scope, an error will be returned.

Tools should generally only show the target features of architectures a that are available within the scope

### x86/x64 (x86_64) [↵](#target_feature-)

Feature               | Implicit features | Description
----------------------|-------------------|-------------
`.adx`                |                   | [ADX](https://en.wikipedia.org/wiki/Intel_ADX) - multi-precision ADd-carry instruction eXtensions
`.aes`                | `.sse2`           | [AES](https://en.wikipedia.org/wiki/AES_instruction_set) - Advanced Encryption Standard
`.avx`                | `.sse4_2`         | [AVX](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) - Advanced Vector eXtensions
`.avx2`               | `.avx`            | [AVX2](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX2) - Advanced Vector eXtensions 2
`.avx512f`            | `.avx2`           | [AVX512F](https://en.wikipedia.org/wiki/AVX-512) - Advanced Vector eXtensions 512 - Foundation
`.avx512cd`           | `.avx512f`        | [AVX512CD](https://en.wikipedia.org/wiki/AVX-512#Conflict_detection) - Advanced Vector eXtensions 512 - 
`.avx512er`           | `.avx512f`        | [AVX512ER](https://en.wikipedia.org/wiki/AVX-512#Exponential_and_reciprocal) - Advanced Vector eXtensions 512 - 
`.avx512pf`           | `.avx512f`        | [AVX512PF](https://en.wikipedia.org/wiki/AVX-512#Prefetch) - Advanced Vector eXtensions 512 - 
`.avx512vl`           | `.avx512f`        | [AVX512VL](https://en.wikipedia.org/wiki/AVX-512) - Advanced Vector eXtensions 512 - Vector Length
`.avx512dq`           | `.avx512f`        | [AVX512DQ](https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI) - Advanced Vector eXtensions 512 - Doubleword and Quadword
`.avx512bw`           | `.avx512f`        | [AVX512BW](https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI) - Advanced Vector eXtensions 512 - Byte and Word
`.avx512ifma`         | `.avx512f`        | [AVX512IFMA](https://en.wikipedia.org/wiki/AVX-512#IFMA) - Advanced Vector eXtensions 512 - Integer Fused Multiply Add
`.avx512vbmi`         | `.avx512f`        | [AVX512VBMI](https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI) - Advanced Vector eXtensions 512 - Vector Byte Manipulation Instructions
`.avx512_4vnni`       | `.avx512f`        | [AVX512_4VNNI](https://en.wikipedia.org/wiki/AVX-512#4FMAPS_and_4VNNIW) - Advanced Vector eXtensions 512 - Vector Neural Network Instrauction Word variable precision
`.avx512_4fmaps`      | `.avx512f`        | [AVX512_4FMAPS](https://en.wikipedia.org/wiki/AVX-512#4FMAPS_and_4VNNIW) - Advanced Vector eXtensions 512 - Fused Multiply Add packed single precision
`.avx512vpopcntdq`    | `.avx512f`        | [AVX512VPOPCNTDQ](https://en.wikipedia.org/wiki/AVX-512#VPOPCNTDQ_and_BITALG) - Advanced Vector eXtensions 512 - Vector POPulation CouNT
`.avx512vnni`         | `.avx512f`        | [AVX512VNNI](https://en.wikipedia.org/wiki/AVX-512#VNNI) - Advanced Vector eXtensions 512 - Vector Neural Network Instructions
`.avx512vbmi2`        | `.avx512f`        | [AVX512VBMI2](https://en.wikipedia.org/wiki/AVX-512#VBMI2) - Advanced Vector eXtensions 512 - Vector Byte Manipulation Instructions 2
`.avx512bitalg`       | `.avx512f`        | [AVX512BITALG](https://en.wikipedia.org/wiki/AVX-512#VPOPCNTDQ_and_BITALG) - Advanced Vector eXtensions 512 - BIT ALGorithms
`.avx512vp2intersect` | `.avx512f`        | [AVX512VP2INTERSECT](https://en.wikipedia.org/wiki/AVX-512#VP2INTERSECT) - Advanced Vector eXtensions 512 - 
`.avx512gfni`         | `.avx512f`        | [AVX512GFNI](https://en.wikipedia.org/wiki/AVX-512#GFNI) - Advanced Vector eXtensions 512 - Galois Field New Instructions
`.avx512vpclmulqdq`   | `.avx512f`        | [AVX512VPCLMULQDQ](https://en.wikipedia.org/wiki/AVX-512#VPCLMULQDQ) - Advanced Vector eXtensions 512 - Vector Carry-Less Multiply Quadword
`.avx512veas`         | `.avx512f`        | [AVX512VEAS](https://en.wikipedia.org/wiki/AVX-512#VAES) - Advanced Vector eXtensions 512 - Vector AES instructions
`.avx512BF16`         | `.avx512f`        | [AVX512BF16](https://en.wikipedia.org/wiki/AVX-512#BF16) - Advanced Vector eXtensions 512 - BFloat16
`.avx512FP61`         | `.avx512f`        | [AVX512FP16](https://en.wikipedia.org/wiki/AVX-512#FP16) - Advanced Vector eXtensions 512 - Half-Precision floating point
`.bmi1`               |                   | [BMI1](https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI1) - Bit Manipulation Instructions set 1
`.bmi2`               |                   | [BMI2](https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI2) - Bit Manipulation Instructions set 2
`.cmpxchg16`          |                   | [cmpxchg16](https://www.felixcloutier.com/x86/cmpxchg8b:cmpxchg16b) CoMPare and eXCHange 16 Bytes (128-bits) of data atomically
`.f16c`               | `.avx`            | [F16C](https://en.wikipedia.org/wiki/F16C) - 16-bit Floating point Conversion instructions
`.fma`                | `.avx`            | [FMA3](https://en.wikipedia.org/wiki/FMA_instruction_set) - 3-operand Fused Multiply-Add
`.fxsr`               |                   | [fxsave](https://www.felixcloutier.com/x86/fxsave) and [fxrstor](https://www.felixcloutier.com/x86/fxrstor) - Save and restore x87 FPU, MMX technology and SSE state
`.lzcnt`              |                   | [lzcnt](https://www.felixcloutier.com/x86/lzcnt) - Leading Zero CouNT
`.movbe`              |                   | [movbe](https://www.felixcloutier.com/x86/movbe) - MOVe data after swapping bytes
`.pclmulqdq`          | `.sse2`           | [pclmulqdq](https://www.felixcloutier.com/x86/pclmulqdq) - Packed Carry-Less Multiply Quadword
`.popcnt`             |                   | [popcnt](https://www.felixcloutier.com/x86/popcnt) - POPulation CouNT
`.rdrand`             |                   | [rdrand](https://en.wikipedia.org/wiki/RDRAND) - ReaD RANDom number
`.rdseed`             |                   | [rdseed](https://en.wikipedia.org/wiki/RDRAND) - ReaD random SEED
`.sha`                | `.sse2`           | [SHA](https://en.wikipedia.org/wiki/Intel_SHA_extensions) - Secure Hash Algorith
`.sse`                |                   | [SSE](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions) - Streaming SIMD Extensions
`.sse2`               | `.sse`            | [SSE2](https://en.wikipedia.org/wiki/SSE2) - Streaming SIMD Extensions 2
`.sse3`               | `.sse2`           | [SSE3](https://en.wikipedia.org/wiki/SSE3) - Streaming SIMD Extensions 3
`.sse4_1`             | `.ssse3`          | [SSE4.1](https://en.wikipedia.org/wiki/SSE4#SSE4.1) - Streaming SIMD Extensions 4.1
`.sse4_2`             | `.sse4_2`         | [SSE4.2](https://en.wikipedia.org/wiki/SSE4#SSE4.2) - Streaming SIMD Extensions 4.2
`.ssse3`              | `.sse3`           | [SSSE3](https://en.wikipedia.org/wiki/SSSE3) - Supplemental Streaming SIMD Extensions 3
`.xsave`              |                   | [xsave](https://www.felixcloutier.com/x86/xsave) - SAVE processor eXtended state
`.xsavec`             |                   | [xsavec](https://www.felixcloutier.com/x86/xsavec) - SAVE processor eXtended states with Compaction
`.xsaveopt`           |                   | [xsaveopt](https://www.felixcloutier.com/x86/xsaveopt) - SAVE processor eXtended state OPTimized
`.xsaves`             |                   | [xsaves](https://www.felixcloutier.com/x86/xsaves) - SAVE processor eXtended sate Supervisor


> _Note_: this list may be incomplete

### arm/aarch64 [↵](#target_feature-)

_TODO_

### riscv [↵](#target_feature-)

_TODO_

## `target_os` [↵](#configuration-options-)

This value defines the operating system the code is being compiled for:
- `.windows`
- `.linux`

## `target_endianness` [↵](#configuration-options-)

This value defines the endianness of the target:
- `.little`
- `.big`

## `target_pointer_width` [↵](#configuration-options-)

This value defines the pointer width on the target:
- `32`
- `64`

## `compilation_mode` [↵](#configuration-options-)

This value defines the compilation mode:
- `.debug`
- `.release`

> _Note_: Additional values can be provided to the compiler

## `assertions` [↵](#configuration-options-)

This value defines whether assertions are enabled:
- `.on`
- `.off`

## `panic` [↵](#configuration-options-)

This value defined the panic mode
- `.unwind`
- `.abort`



[conditional compilation attributes]: ./attributes.md#conditional-compilation-attributes-
[`when` expressions]:                 ./expressions/when-expressions.md)
[ADX]:                                https://en.wikipedia.org/wiki/Intel_ADX
[AES]:                                https://en.wikipedia.org/wiki/AES_instruction_set
[AVX]:                                https://en.wikipedia.org/wiki/Advanced_Vector_Extensions
[AVX2]:                               https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX2
[AVX512F]:                            https://en.wikipedia.org/wiki/AVX-512
[AVX512CD]:                           https://en.wikipedia.org/wiki/AVX-512#Conflict_detection
[AVX512ER]:                           https://en.wikipedia.org/wiki/AVX-512#Exponential_and_reciprocal
[AVX512PF]:                           https://en.wikipedia.org/wiki/AVX-512#Prefetch
[AVX512VL]:                           https://en.wikipedia.org/wiki/AVX-512
[AVX512DQ]:                           https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI
[AVX512BW]:                           https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI
[AVX512IFMA]:                         https://en.wikipedia.org/wiki/AVX-512#IFMA
[AVX512VBMI]:                         https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI
[AVX512_4VNNI]:                       https://en.wikipedia.org/wiki/AVX-512#4FMAPS_and_4VNNIW
[AVX512_4FMAPS]:                      https://en.wikipedia.org/wiki/AVX-512#4FMAPS_and_4VNNIW
[AVX512VPOPCNTDQ]:                    https://en.wikipedia.org/wiki/AVX-512#VPOPCNTDQ_and_BITALG
[AVX512VNNI]:                         https://en.wikipedia.org/wiki/AVX-512#VNNI
[AVX512VBMI2]:                        https://en.wikipedia.org/wiki/AVX-512#VBMI2
[AVX512BITALG]:                       https://en.wikipedia.org/wiki/AVX-512#VPOPCNTDQ_and_BITALG
[AVX512VP2INTERSECT]:                 https://en.wikipedia.org/wiki/AVX-512#VP2INTERSECT
[AVX512GFNI]:                         https://en.wikipedia.org/wiki/AVX-512#GFNI
[AVX512VPCLMULQDQ]:                   https://en.wikipedia.org/wiki/AVX-512#VPCLMULQDQ
[AVX512VEAS]:                         https://en.wikipedia.org/wiki/AVX-512#VAES
[AVX512BF16]:                         https://en.wikipedia.org/wiki/AVX-512#BF16
[AVX512FP16]:                         https://en.wikipedia.org/wiki/AVX-512#FP16
[BMI1]:                               https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI1
[BMI2]:                               https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI2
[cmpxchg16]:                          https://www.felixcloutier.com/x86/cmpxchg8b:cmpxchg16b
[F16C]:                               https://en.wikipedia.org/wiki/F16C
[FMA3]:                               https://en.wikipedia.org/wiki/FMA_instruction_set
[fxsave]:                             https://www.felixcloutier.com/x86/fxsave
[fxrstor]:                            https://www.felixcloutier.com/x86/fxrstor
[lzcnt]:                              https://www.felixcloutier.com/x86/lzcnt
[movbe]:                              https://www.felixcloutier.com/x86/movbe
[pclmulqdq]:                          https://www.felixcloutier.com/x86/pclmulqdq
[popcnt]:                             https://www.felixcloutier.com/x86/popcnt
[rdrand]:                             https://en.wikipedia.org/wiki/RDRAND
[rdseed]:                             https://en.wikipedia.org/wiki/RDRAND
[SHA]:                                https://en.wikipedia.org/wiki/Intel_SHA_extensions
[SSE]:                                https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions
[SSE2]:                               https://en.wikipedia.org/wiki/SSE2
[SSE3]:                               https://en.wikipedia.org/wiki/SSE3
[SSE4.1]:                             https://en.wikipedia.org/wiki/SSE4#SSE4.1
[SSE4.2]:                             https://en.wikipedia.org/wiki/SSE4#SSE4.2
[SSSE3]:                              https://en.wikipedia.org/wiki/SSSE3
[xsave]:                              https://www.felixcloutier.com/x86/xsave
[xsavec]:                             https://www.felixcloutier.com/x86/xsavec
[xsaveopt]:                           https://www.felixcloutier.com/x86/xsaveopt
[xsaves]:                             https://www.felixcloutier.com/x86/xsaves