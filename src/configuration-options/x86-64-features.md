# X86/64 Features

This page contains a table with all currently supported x86/64 features.

Feature              | Implicit features  | Description
---------------------|--------------------|------------------------------------------------------------------------------------------------------------------------------------------------
`abm`                | `lzcnt`, `popcnt`  | [ABM] - **A**dvanced **B**it **M**anipulation - combined `pocnt` and `lzcnt`
`adx`                |                    | [ADX] - multi-precision ADd-carry instruction eXtensions
`aes`                | `sse2`             | [AES] - Advanced Encryption Standard
`aeskle`             | `aes`, `kl`        | [AESKLE] - Advanded Encryption Standard Key Locker instuctions
`amx-bf16`           |                    | [AMX]-bf16 - Advanced Matrix eXtensions - BFloat-16 instructions
`amx-complex`        |                    | [AMX]-tile - Advanced Matrix eXtensions - Complex number instructions
`amx-fp16`           |                    | [AMX]-fp16 - Advanced Matrix eXtensions - 16-bit Floating Point instructions
`amx-int8`           |                    | [AMX]-int8 - Advanced Matrix eXtensions - 8-bit INTeger instructions
`amx-tile`           |                    | [AMX]-tile - Advanced Matrix eXtensions - Tile load/store instructions
`amx-fp8`            |                    | [AMX]-fp8 - Advanced Matrix eXtensions - 8-bit Floating Point instructions (no public information available at this moment)
`amx-tf32`           |                    | [AMX]-tf32 - Advanced Matrix eXtensions - TensorFloat-32 & 16-bit Floating point instructions (no public information available at this moment)
`amx-avx512`         |                    | [AMX]-avx512 - Advanced Matrix eXtensions - AVX512 instucitons (no public information available at this moment)
`amx-movrs`          |                    | [AMX]-movrs - Advanced Matrix eXtensions - MOVRS instruction (no public information available at this moment)
`avx`                | `sse4_2`           | [AVX] - Advanced Vector eXtensions
`avx_ifma`           | `avx`              | [AVX-IFMA] - Advanced Vector eXtensions - Integer Fused Multiply Add
`avx_ne_convert`     | `avx`              | [AVX-NE-CONVERT] - Advanced Vector eXtensions - NO-exception CONVERT
`avx_vnni`           | `avx`              | [AVX-VNNI] - Advanced Vector eXtensions - Vector Neural Network Instructions
`avx_vnni_int8`      | `avx`              | [AVX-VNNI-INT8] - Advanced Vector eXtensions - Vector Neural Network Instructions - 8-bit INTeger 
`avx_vnni_int16`     | `avx`              | [AVX-VNNI-INT16] - Advanced Vector eXtensions - Vector Neural Network Instructions - 16-bit INTeger 
`avx2`               | `avx`              | [AVX2] - Advanced Vector eXtensions 2
`avx10_1`            |                    | [AVX10] - Advanced Vector eXtensions 10.1
`avx10_2`            |                    | [AVX10] - Advanced Vector eXtensions 10.2
`avx512f`            | `avx2`             | [AVX512F] - Advanced Vector eXtensions 512 - Foundation
`avx512cd`           | `avx512f`          | [AVX512CD] - Advanced Vector eXtensions 512 - Conflict Detection
`avx512er`           | `avx512f`          | [AVX512ER] - Advanced Vector eXtensions 512 - Exponential and Reciprocal
`avx512pf`           | `avx512f`          | [AVX512PF] - Advanced Vector eXtensions 512 - PreFetch
`avx512vl`           | `avx512f`          | [AVX512VL] - Advanced Vector eXtensions 512 - Vector Length
`avx512dq`           | `avx512f`          | [AVX512DQ] - Advanced Vector eXtensions 512 - Doubleword and Quadword
`avx512bw`           | `avx512f`          | [AVX512BW] - Advanced Vector eXtensions 512 - Byte and Word
`avx512ifma`         | `avx512f`          | [AVX512IFMA] - Advanced Vector eXtensions 512 - Integer Fused Multiply Add
`avx512vbmi`         | `avx512f`          | [AVX512VBMI] - Advanced Vector eXtensions 512 - Vector Byte Manipulation Instructions
`avx512_4vnni`       | `avx512f`          | [AVX512_4VNNI] - Advanced Vector eXtensions 512 - Vector Neural Network Instruction Word variable precision
`avx512_4fmaps`      | `avx512f`          | [AVX512_4FMAPS] - Advanced Vector eXtensions 512 - Fused Multiply Add packed single precision
`avx512vpopcntdq`    | `avx512f`          | [AVX512VPOPCNTDQ] - Advanced Vector eXtensions 512 - Vector POPulation CouNT
`avx512vnni`         | `avx512f`          | [AVX512VNNI] - Advanced Vector eXtensions 512 - Vector Neural Network Instructions
`avx512vbmi2`        | `avx512f`          | [AVX512VBMI2] - Advanced Vector eXtensions 512 - Vector Byte Manipulation Instructions 2
`avx512bitalg`       | `avx512f`          | [AVX512BITALG] - Advanced Vector eXtensions 512 - BIT ALGorithms
`avx512vp2intersect` | `avx512f`          | [AVX512VP2INTERSECT] - Advanced Vector eXtensions 512 - 
`avx512gfni`         | `avx512f`          | [AVX512GFNI] - Advanced Vector eXtensions 512 - Galois Field New Instructions
`avx512vpclmulqdq`   | `avx512f`          | [AVX512VPCLMULQDQ] - Advanced Vector eXtensions 512 - Vector Carry-Less Multiply Quadword
`avx512veas`         | `avx512f`          | [AVX512VEAS] - Advanced Vector eXtensions 512 - Vector AES instructions
`avx512BF16`         | `avx512f`          | [AVX512BF16] - Advanced Vector eXtensions 512 - BFloat16
`avx512FP61`         | `avx512f`          | [AVX512FP16] - Advanced Vector eXtensions 512 - Half-Precision floating point
`avx512BMM`          | `avx512f`          | [AVX512BMM] - Advanced Vector eXtensions 512 - Bit Manipulation
`apx`                |                    | [APX] - Advanced Performance Extensions
`bmi1`               |                    | [BMI1] - Bit Manipulation Instructions set 1
`bmi2`               |                    | [BMI2] - Bit Manipulation Instructions set 2
`cldemote`           |                    | [`cldemote`] - Cache Line Demote
`clflushopt`         |                    | [`clflushopt`] - Cache-Line FLUSH OPTimized
`clwb`               |                    | [`clwb`] - Cache-Line Write Back
`cmpccxadd`          | `avx2`             | [`CMPccXADD`] instructions
`cmpxchg16`          |                    | [`cmpxchg16`] CoMPare and eXCHange 16 Bytes (128-bits) of data atomically
`enqcmd`             |                    | [`enqcmd`], [`enqcmds`] - ENQueue CoMmanD (Supervisor)
`f16c`               | `avx`              | [F16C] - 16-bit Floating point Conversion instructions
`fma`                | `avx`              | [FMA3] - 3-operand Fused Multiply-Add
`fsgsbase`           |                    | [`fsgsbase`] - Read/write base address of GS and FS segment registers
`fxsr`               |                    | [`fxsave`] and [`fxrstor`] - Save and restore x87 FPU, MMX technology and SSE state
`gnfi`               | `sse4_2`           | [GFNI] - SSE and AVX variants of [AVX512GFNI]
`kl`                 |                    | [KL] - Key Locker common instructions
`lahf_sahf`          |                    | [`lahf`] and [`sahf`] support in long mode
`lzcnt`              |                    | [`lzcnt`] - Leading Zero CouNT
`mmx`                |                    | [MMX] - MultiMedia eXtensions
`movbe`              |                    | [`movbe`] - MOVe data after swapping bytes
`movdiri`            |                    | [`movdiri`] - MOVe doubleword as DIRect store
`movdir64b`          |                    | [`movdir64b`] - MOVE 64 Bytes as DIRect store
`osxsave`            |                    | [OSXSAVE] - XSAVE enabled by OS
`pclmulqdq`          | `sse2`             | [`pclmulqdq`] - Packed Carry-Less Multiply Quadword
`pconfig`            |                    | [`pconfig`] - Platform Configuration
`popcnt`             |                    | [`popcnt`] - POPulation CouNT
`prefetchw`          |                    | [`prefetchw`] PREFETCH data into caches in anticipation of a Write
`prefetchi`          |                    | [PREFETCHI] - PREFETCH instruction
`ptwrite`            |                    | [`ptwrite`]: WRITE data to a Processor Trace packet
`raoint`             |                    | `aadd`, `aand`, `aor`, `axor` - Remote Atomic Operations on INTegers
`rdrand`             |                    | [`rdrand`] - ReaD RANDom number
`rdpid`              |                    | [`rdpid`] - ReaD Processor ID
`rdseed`             |                    | [`rdseed`] - ReaD random SEED
`serialize`          |                    | [`serialize`] - Serialize instuction execution instruction
`sha`                | `sse2`             | [SHA] - Secure Hash Algorithm (SHA-1 and 256 bit)
`sha512`             | `avx`              | [SHA] - 512-bit Secure Hash Algorithm
`sse`                | `mmx`              | [SSE] - Streaming SIMD Extensions
`sse2`               | `sse`              | [SSE2] - Streaming SIMD Extensions 2
`sse3`               | `sse2`             | [SSE3] - Streaming SIMD Extensions 3
`sse4`               | `sse4_1`, `sse4_2` | combined [SSE4.1] and [SSE4.2] (mainly useful in feature negation)
`sse4_1`             | `ssse3`            | [SSE4.1] - Streaming SIMD Extensions 4.1
`sse4_2`             | `sse4_2`           | [SSE4.2] - Streaming SIMD Extensions 4.2
`ssse3`              | `sse3`             | [SSSE3] - Supplemental Streaming SIMD Extensions 3
`sm3`                | `avx`              | [SM3] - ShangMi 3 cryptographic extension
`sm4`                | `avx`              | [SM4] - ShangMi 4 cryptographic extension
`uintr`              |                    | [UINTR] - User INTeRprocessor interrupt
`user_msr`           |                    | [USER_MSR] - User-mode MSR access
`vaes`               |                    | [VAES] - Vectore Advanced Encryption Standard
`vpclmulqdq`         |                    | [`vpclmulqdq`] - Vector Packed Carry-Less Multiply Quadword
`waitpkg`            |                    | [`tpause`], [`umonitor`], [`umwait`] - Timed pasue and user-level monitor/wait instructions
`wbnoinvd`           |                    | [`wbnoinvd`] - Write Back and do NOt INValidate cache
`wide_kl`            | `easkle`           | [WIDEKL] - Wide Key Lock instructions
`xsave`              |                    | [`xsave`], [`xrstor`], [`xsetbv`], `xgetbv` - rocessor eXtended state instructions
`xsavec`             |                    | [`xsavec`] - SAVE processor eXtended states with Compaction
`xsaveopt`           |                    | [`xsaveopt`] - SAVE processor eXtended state OPTimized
`xsaves`             |                    | [`xsaves`] - SAVE processor eXtended sate Supervisor

> _Note_: In the future this table will be generated from compiler provided data

[ABM]:                                https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#ABM_(Advanced_Bit_Manipulation)
[ADX]:                                https://en.wikipedia.org/wiki/Intel_ADX
[AES]:                                https://en.wikipedia.org/wiki/AES_instruction_set
[AESKLE]:                             https://en.wikipedia.org/wiki/List_of_x86_cryptographic_instructions#:~:text=AESKLE
[AMX]:                                https://en.wikipedia.org/wiki/Advanced_Matrix_Extensions
[APX]:                                https://en.wikipedia.org/wiki/X86#APX_(Advanced_Performance_Extensions)
[AVX]:                                https://en.wikipedia.org/wiki/Advanced_Vector_Extensions
[AVX-IFMA]:                           https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX-IFMA
[AVX-NE-CONVERT]:                     https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX-NE-CONVERT
[AVX-VNNI]:                           https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX-VNNI
[AVX-VNNI-INT8]:                      https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX-VNNI-INT8
[AVX-VNNI-INT16]:                     https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX-VNNI-INT16
[AVX2]:                               https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX2
[AVX10]:                              https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX10
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
[AVX512BMM]:                          https://en.wikipedia.org/wiki/AVX-512#BMM
[BMI1]:                               https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI1
[BMI2]:                               https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI2
[`cldemote`]:                         https://www.felixcloutier.com/x86/cldemote
[`clflushopt`]:                       https://www.felixcloutier.com/x86/clflushopt
[`clwb`]:                             https://www.felixcloutier.com/x86/clwb
[`CMPccXADD`]:                        https://en.wikipedia.org/wiki/List_of_x86_instructions#:~:text=CMPccXADD
[`cmpxchg16`]:                        https://www.felixcloutier.com/x86/cmpxchg8b:cmpxchg16b
[F16C]:                               https://en.wikipedia.org/wiki/F16C
[FMA3]:                               https://en.wikipedia.org/wiki/FMA_instruction_set
[`fsgsbase`]:                         https://en.wikipedia.org/wiki/List_of_x86_instructions#:~:text=fsgsbase
[`fxsave`]:                           https://www.felixcloutier.com/x86/fxsave
[`fxrstor`]:                          https://www.felixcloutier.com/x86/fxrstor
[GFNI]:                               https://en.wikipedia.org/wiki/AVX-512#GFNI
[`hreset`]:                           https://www.felixcloutier.com/x86/hreset
[`enqcmd`]:                           https://www.felixcloutier.com/x86/enqcmd
[`enqcmds`]:                          https://www.felixcloutier.com/x86/enqcmds
[KL]:                                 https://www.felixcloutier.com/x86/loadiwkey
[`lahf`]:                             https://www.felixcloutier.com/x86/lahf
[`lzcnt`]:                            https://www.felixcloutier.com/x86/lzcnt
[MMX]:                                https://en.wikipedia.org/wiki/MMX_(instruction_set)
[`movbe`]:                            https://www.felixcloutier.com/x86/movbe
[`movdir64b`]:                        https://www.felixcloutier.com/x86/movdir64b
[`movdiri`]:                          https://www.felixcloutier.com/x86/movdiri
[`movrs`]:                            https://en.wikipedia.org/wiki/CPUID#:~:text=movrs
[OSXSAVE]:                            https://en.wikipedia.org/wiki/CPUID#:~:text=osxsave
[`pclmulqdq`]:                        https://www.felixcloutier.com/x86/pclmulqdq
[`pconfig`]:                          https://www.felixcloutier.com/x86/pconfig
[`popcnt`]:                           https://www.felixcloutier.com/x86/popcnt
[PREFETCHI]:                          https://en.wikipedia.org/wiki/List_of_x86_instructions#:~:text=PREFETCHI
[`prefetchw`]:                        https://www.felixcloutier.com/x86/prefetchw
[`ptwrite`]:                          https://www.felixcloutier.com/x86/ptwrite
[`rdrand`]:                           https://en.wikipedia.org/wiki/RDRAND
[`rdpid`]:                            https://www.felixcloutier.com/x86/rdpid
[`rdseed`]:                           https://en.wikipedia.org/wiki/RDRAND
[`sahf`]:                             https://www.felixcloutier.com/x86/sahf
[`serialize`]:                        https://www.felixcloutier.com/x86/serialize
[SHA]:                                https://en.wikipedia.org/wiki/SHA_instruction_set
[SHA1]:                               https://en.wikipedia.org/wiki/SHA_instruction_set
[SM3]:                                https://en.wikipedia.org/wiki/SM3_(hash_function)
[SM4]:                                https://en.wikipedia.org/wiki/SM4_(cipher)
[SSE]:                                https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions
[SSE2]:                               https://en.wikipedia.org/wiki/SSE2
[SSE3]:                               https://en.wikipedia.org/wiki/SSE3
[SSE4.1]:                             https://en.wikipedia.org/wiki/SSE4#SSE4.1
[SSE4.2]:                             https://en.wikipedia.org/wiki/SSE4#SSE4.2
[SSSE3]:                              https://en.wikipedia.org/wiki/SSSE3
[`tpause`]:                           https://www.felixcloutier.com/x86/tpause
[UINTR]:                              https://en.wikipedia.org/wiki/List_of_x86_instructions#:~:text=uintr
[`umonitor`]:                         https://www.felixcloutier.com/x86/umonitor
[`umwait`]:                           https://www.felixcloutier.com/x86/umwait
[USER_MSR]:                           https://en.wikipedia.org/wiki/CPUID#:~:text=user_msr
[VAES]:                               https://en.wikipedia.org/wiki/AVX-512#VAES
[`vpclmulqdq`]:                       https://www.felixcloutier.com/x86/pclmulq
[`wbnoinvd`]:                         https://www.felixcloutier.com/x86/wbnoinvd
[WIDEKL]:                             https://en.wikipedia.org/wiki/List_of_x86_cryptographic_instructions#:~:text=WIDE_KL
[`xsave`]:                            https://www.felixcloutier.com/x86/xsave
[`xrstor`]:                           https://www.felixcloutier.com/x86/xrstor
[`xsetbv`]:                           https://www.felixcloutier.com/x86/xsetbv
[`xsavec`]:                           https://www.felixcloutier.com/x86/xsavec
[`xsaveopt`]:                         https://www.felixcloutier.com/x86/xsaveopt
[`xsaves`]:                           https://www.felixcloutier.com/x86/xsaves
