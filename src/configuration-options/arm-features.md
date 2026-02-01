## ARM features

This page contains a table with all currently supported ARM features.

Feature         | Implicit features                                           | AArch64 only | Description
----------------|-------------------------------------------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------------
`afp`           | `neon`                                                      | X            | `FEAT_AFP` - Alternative Float-Point Handling
`bf16`          | `neon`                                                      | X            | `FEAT_BF16` - BFloat16 instructions
`bti`           |                                                             | X            | `FEAT_BTI` - [BTI] - Branch Target Identification
`crc32`         |                                                             |              | `FEAT_CRC32` - 32-bit [CRC] instructions
`crypto`        |                                                             |              | `FEAT_AES`/`FEAT_SHA1` - Advanced Encryption Standard ([AES]) & Secure Hashing Algorithm 1 ([SHA1])
`crypto_2`      | `v8_2`, `pmull`, `sha256`                                   |              | `FEAT_SM3`/`FEAT_SM4` - Crypto v8.2 extensions - combined `crypto`, `pmull`, `sha256`, `sm3`, `sm4`
`crypto_v9`     | `crypto_2`, `sha3`, `sha512`                                | X            | `FEAT_Armv9_Crypto` - Crypto v9 extensions - combined, `crypto_v2`, `sha3`, `ssha512`
`cmpbr`         |                                                             | X            | `FEAT_CMPBR` - CoMPare and BRanch instruction
`dit`           |                                                             |              | `FEAT_DIT` - Data Independent Timing instructions
`dotprod`       | `neon`                                                      |              | `FEAT_DOTPROD` - Advanced SIMD DOT PRODuct instructions
`dpb`           |                                                             | X            | `FEAT_DPB` - Clean data cache by address to Point of Persistance
`ebf16`         | `bf16`                                                      | X            | `FEAT_EBF16` - Extended BFloat16 behaviors
`faminmax`      | `neon`, `sve2`, or `sme2`                                   |              | `FEAT_FAMINMAX` - Floating-point MIMimum and MAXimimum Absolute value instructions
`fcma`          | `neon`                                                      |              | `FEAT_FCMA` - Floating-point Complex number instructions
`fhm`           | `fp16`                                                      |              | `FEAT_FHM` - Floating-point Half-precision to single-precision Multipy-add instructions
`flagm`         |                                                             | X            | `FEAT_FlagM` - Condition Flag Manipulation instructions
`fpmr`          | `neon`                                                      |              | `FEAT_FPMR` - Floating-point Mode Register
`fprcvt`        | `neon`                                                      | X            | `FEAT_FPRCVT` - Floating-point to/from integer in scalar fp register
`fp8`           | `fpmr`, `faminmax`, `lut`, `bf16`, `neon`, `sve2` or `sme2` |              | `FEAT_FP8` - FP8 convert instructions
`fp8dot2`       | `fp8dot4`                                                   |              | `FEAT_FP8DOT2` - FP8 2-way dot product to half-precision instructions
`fp8dot4`       | `fp8fma`                                                    |              | `FEAT_FP8DOT4` - FP8 4-way dot product to single-precision instuctions
`fp8fma`        | `fp8`                                                       |              | `FEAT_FP8FMA` - FP8 Multiply-Accumulate to half-precision and single-precision instructions
`fp16`          | `fhm` (v8.4 or above)                                       |              | `FEAT_FP16` - Half-precision Floating-Point
`frintts`       |                                                             | X            | `FEAT_FRINTTS` - Floating-point to INTeger instuctionsns
`f32mm`         | `sve`                                                       | X            | `FEAT_F32MM` - Single-precision Floating-point Matrix Multiply-accumulate
`f64mm`         | `sve`                                                       | X            | `FEAT_F32MM` - Double-precision Floating-point Matrix Multiply-accumulate
`f8f16mm`       | `f8f32mm`, `fpdot2`                                         | X            | `FEAT_F8F16MM` - 8-bit floating-point Matrix Multiply-accumulate to half-precision
`f8f32mm`       | `fp8dot4`                                                   | X            | `FEAT_F8F32MM` - 8-bit floating-point Matrix Multiply-accumulate to single-precision
`hbc`           |                                                             | X            | `FEAT_HBC` - Hinted Conditional Branches
`i8mm`          |                                                             |              | `FEAT_AA32I8MM`/`FEAT_I8MM` - Int8 Matric Multiple Accumulate
`jscvt`         | `neon`                                                      |              | `FEAT_JSCVT` - JavaScript ConVerT instructions
`lor`           |                                                             | X            | `FEAT_LOR` - Limited Ordering Regions ([LOR]) (atomics)
`lse`           |                                                             | X            | `FEAT_LSE` - Large System Extensions ([LSE]) (atomics)
`lse128`        | `lse`                                                       | X            | `FEAT_LSE128` - 128-bit atomics
`lsfe`          | `neon`                                                      | X            | `FEAT_LSFE` - LSE float extensions
`ls64`          |                                                             | X            | `FEAT_LS64` - 64-byte Load and Stores without status
`ls64v`         | `ls64`                                                      | X            | `FEAT_LS64_V`- 64-byte Store with status
`lut`           | `neon`, `sve2`, or `sme2`                                   |              | `FEAT_LUT` - Look-Up Table instructions with 2-bit and 4-bit indices
`mte`           |                                                             | X            | `FEAT_MTE` & `FEAT_MTE2` - Memory Tagging Extension 
`neon`          |                                                             |              | [NEON]/`FEAT_AdvSIMD`/`FEAT_FP` - FP & Advanced SIMD Instructions
`pan`           |                                                             |              | `FEAT_PAN` - Privileged Access Never
`pan2`          |                                                             |              | `FEAT_PAN2` - `AT S1E1R` and `AT S1E1W` instruction variants affected by `PSTATE.PAN`
`pauth_ap`      |                                                             | X            | `FEAT_PAUTH` - Pointer authentication - Address Pointer Authentication
`pauth_gp`      |                                                             | X            | `FEAT_PAUTH` - Pointer authentication - Generic Pointer Authentication
`pmu_v3`        |                                                             |              | `FEAT_PMUv3` - Performance Monitor extensions v3
`pmull`         |                                                             |              | `FEAT_PMULL` - [AES] Polynomial instructions
`ras`           |                                                             |              | `FEAT_RAS` - Reliability, Availability and Serviceability (RAS) extension
`rcpc`          |                                                             | X            | `FEAT_LRCPC` - Load-acquire RCPC (Release Consistency Processor Consistent) instructions
`rcpc2`         |                                                             | X            | `FEAT_LRCPC2` - Load-acquite RCPC with 9-bit unscalled immediate offset
`rdm`           | `neon`                                                      |              | `FEAT_RDM` - NEON Rounding Multipy Accumulate instructions
`rng`           |                                                             | X            | `FEAT_RNG` - Random Number Generator
`rpres`         | `afp`                                                       | X            | `FEAT_RPRES` - Increased precision for `FRCEPE` and `FRSQRTE` (estimates increased from 8-bit to 12-bit mantissa)
`sb`            |                                                             |              | `FEAT_SB` - Speculation Barier
`sha3`          | `crypto`, `sha256`                                          | X            | `FEAT_SHA3` - Secure Hashing Algorithm 3 ([SHA])
`sha256`        | `crypto`                                                    |              | `FEAT_SHA256` - 256-bit Secure Hashing Algorithm ([SHA])
`sha512`        | `crypto`, `sha256`                                          | X            | `FEAT_SHA512` - 512-bit Secure Hashing Algorithm ([SHA])
`sm3`           | `crypto`                                                    | X            | `FEAT_SM3` - ShangMi 3 ([SM3])
`sm4`           | `crypto`                                                    | X            | `FEAT_SM4` - ShangMi 4 ([SM4])
`spe`           | `pmu_v3`                                                    | X            | `FEAT_SPE` - Statistical Profiling Extension
`ssve_aes`      | `SME2_1`                                                    | X            | `FEAT_SSVE_AES` - Streaing [SVE] mode AES and 128-bit polynomial multiply long instructions
`ssve_bitperm`  | `SME2_1`                                                    | X            | `FEAT_SSVE_BitPerm` - Streaming [SVE] mode Bit Permute instrucitons
`ssve_fexpa`    | `SME2_1`                                                    | X            | `FEAT_SSVE_FEXPA` - Streaming [SVE] FEXPA instruction
`ssve_fp8dot2`  | `ssve_fp8dot4`                                              |              | `FEAT_SSVE_FP8DOT2` - [SVE2] FP8 2-way dot product to half-precision instructions in streaming sve mode
`ssve_fp8dot4`  | `ssve_fp8fma`                                               |              | `FEAT_SSVE_FP8DOT4` - [SVE2] FP8 4-way dot product to single-precision instuctions in streaming sve mode
`ssve_fp8fma`   | `fp8`, `sme2`                                               |              | `FEAT_SSVE_FP8fma` - [SVE2] FP8 Multiply-Accumulate to half-precision and single-precision instructions in streaming sve mode
`sve`           | `fp16`, `fcma`                                              | X            | `FEAT_SVE` - [SVE] - Scalable Vector Extension
`sve2`          | `sve`                                                       | X            | `FEAT_SVE2` - [SVE] - Scalable Vector Extensions 2
`sve2_1`        | `sve2`                                                      | X            | `FEAT_SVE2p1` - [SVE] - Scalable Vector Extensions 2.1
`sve2_2`        | `sve2_1`                                                    | X            | `FEAT_SVE2_2` - [SVE] - Scalable Vector Extensions 2.2
`sve_aes`       | `sve`, `crypto`                                             | X            | `FEAT_SVE_AES` - [SVE] variants of AES instructions
`sve_aes2`      | `sve_pmull128`                                              | X            | `FEAT_SVE_AES2` - [SVE] multi-vector AES and 128-bit polynomial multiply long instructions
`sve_bfscale`   | `sve_b16b16`                                                | X            | `FEAT_SVE_BFSCALE` - [SVE] - BFloat16 Floating-Point adjust exponent
`sve_bitperm`   | `sve`                                                       | X            | `FEAT_SVE_BitPerm` - [SVE] - Scalable Vector Bit Permutes instructions
`sve_b16b16`    | `sve2` or `sme2`                                            | X            | `FEAT_SVE_B16B16` - [SME] and SVE2 non-widening BFloat16 to BFloat16 arithmetic
`sve_f16f32mm`  | `sve2_2`                                                    | X            | `FEAT_SVE_F16F32MM` - [SVE] Half-precision floating-point matrix multiply-accumulate to single-precision
`sve_pmull128`  | `sve`, `sve_aes`                                            | X            | `FEAT_SVE_PMULL128` - [SVE] single-vector AES and 128-bit polynomial multiply long instructions
`sve_sha3`      | `sve`, `crypto_v9`                                          | X            | `FEAT_SVE_SHA3` - [SVE] - Scalable Vector [SHA3] extensions
`sve_sm4`       | `sve`, `sm4`                                                | X            | `FEAT_SVE_SM4` - [SVE] - Scalable Vector [SM4] extensions
`sme`           | `fcma`, `fp16`, `bf16`, `bhf`                               | X            | `FEAT_SME` - Scalable Matrix Extensions
`sme2`          | `sme`                                                       | X            | `FEAT_SME2` - Scalable Matrix Extensions 2
`sme2_1`        | `sme2`                                                      | X            | `FEAT_SME2p1` - Scalable Matrix Extensions 2.1
`sme2_2`        | `sme_mop4`, `sme_tmop`, `ssve_bitperm`, `ssve_fexpa`        | X            | `FEAT_SME2p2` Scalable Matrix Extensions 2.2
`sme_b16b16`    | `sme2`, `sve_b16b16`                                        | X            | `FEAT_SME_B16B16` - [SME] non-widening BFloat16 to BFloat16 ZA-targeting arithmatic
`sme_fa64`      | `sme`, `sve2`                                               | X            | `FEAT_SME_FA64` - [SME] Full A64 instruction set support in streaming SVE mode
`sme_f8f16`     | `sme_f8f32`                                                 |              | `FEAT_SME_F8F16` - [SME] ZA-targeting FP8 multiply-accumulate, dot product, and out product to half-precision instructions
`sme_f8f32`     | `sme2`, `fp8`                                               |              | `FEAT_SME_F8F32` - [SME] ZA-targeting FP8 multiply-accumulate, dot product, and out product to single-precision instructions
`sme_f16f16`    | `sme2`                                                      | X            | `FEAT_SME_F16F16` - [SME] non-widening half-precision FP16 to FP16 arithmetic
`sme_f64f64`    | `sme`, `sve2`                                               | X            | `FEAT_SME_F64F64` - [SME] double-precision floating-point outer product instructions
`sme_i16i64`    | `sme`, `sve2`                                               | X            | `FEAT_SME_I16I64` - [SME] 16-bit to 64-bit integer widening outer product instuctions
`sme_lut_v2`    | `sme`                                                       |              | `FEAT_SME_LUTv2` - Look-Up Table instructions with 4-bit and 8-bit elements
`sme_mop4`      | `sme2_1`                                                    |              | `FEAT_SME_MOP4` - [SME] Quarter-tile outer product instructions
`sme_tmop`      | `sme2_1`                                                    |              | `FEAT_SME_TMOP` - [SME] Structured sparsity outer product instructions
`tme`           |                                                             | X            | `FEAT_TME` - Transactional Memory Extensions
`vhe`           | `lse`                                                       |              | `FEAT_VHE` - Vritualization Host Extension


> _Note_: In the future this table will be generated from compiler provided data

> _Todo_: Link to relevant documentation for features


[AES]:  https://en.wikipedia.org/wiki/AES_instruction_set
[BTI]:  https://en.wikipedia.org/wiki/Indirect_branch_tracking
[CRC]:  https://en.wikipedia.org/wiki/Cyclic_redundancy_check
[LOR]:  https://developer.arm.com/documentation/102336/0100/Load-Acquire-and-Store-Release-instructions#:~:text=Limited%20Ordering%20Regions
[LSE]:  https://learn.arm.com/learning-paths/servers-and-cloud-computing/lse/intro/
[NEON]: https://developer.arm.com/Architectures/Neon
[SHA]:  https://en.wikipedia.org/wiki/SHA_instruction_set
[SHA3]: https://en.wikipedia.org/wiki/SHA_instruction_set
[SME]:  https://developer.arm.com/community/arm-community-blogs/b/architectures-and-processors-blog/posts/arm-scalable-matrix-extension-introduction
[SM3]:  https://en.wikipedia.org/wiki/SM3_(hash_function)
[SM4]:  https://en.wikipedia.org/wiki/SM4_(cipher)
[SVE]:  https://developer.arm.com/documentation/102476/0101/Introducing-SVE
[SVE2]: https://developer.arm.com/documentation/102476/0101/Introducing-SVE