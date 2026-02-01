# RISC-V Features

This page contains a table with all current supported RISC-V features

Features       | Implicit features                              | Description
---------------|------------------------------------------------|---------------------------------------------------------------------------------------------
`a`            | `zaamo`, `zalrsc`                              | Atomic instructions
`bf16`         | `zfhmin`                                       | BFLoat16 floating point
`b`            | `zba`, `zbb`, `zbs`                            | Bit manipulation
`c`            | `zc*`                                          | Compressed instructions
`cmo`          |                                                | Base cache Management operations
`d`            | `f`                                            | Double-precision floating-point
`f`            |                                                | Single-precision floating-point
`h`            |                                                | Hypervisor support
`m`            | `Zmmul`                                        | Integer multiplication and division
`q`            | `d`                                            | Quad-precision floating-point
`v`            | `d`                                            | Vector operations
`shcounterenw` |                                                | Hypervisor support writable enables for any suppored counter
`shgatpa`      |                                                | XvNNx4 mode supported for all modes supported by `stp`, as well as bare
`shtvala`      |                                                | `tval` provides all needed values
`shvstvala`    |                                                | Hypervisor `stval` provides all needed values
`shvstvecd`    |                                                | Hypervisor `stvec` supports direct mode
`shvsatpa`     |                                                | `vsatp` suppors all modes supported by `satp`
`smcdeleg`     |                                                | Counter Delegation extension
`smcntrpmf`    |                                                | Cycle and Instret Priviledge Mode Filtering
`smcsrind`     |                                                | Machine Indirect CSR access
`smctr`        |                                                | Control Transfer Records extension
`smdbltrp`     |                                                | Machine Double Trap extension
`smepmp`       |                                                | PMP Enhancements for memory access and execution prevsion in machine mode
`smmpm`        |                                                | Disable pointer masking for M mode
`smnpm`        |                                                | Supervisor disable pointer masking for the next priviledge mode
`smrnmi`       |                                                | Resumable Non-Maskable Interrupts
`smstateen`    |                                                | Machine State Enable extension
`ssccptr`      |                                                | Main memory supports page table reads
`sscofpmf`     |                                                | Counter OverFlow and Mode-based Filtering
`sscounterenw` |                                                | Supervisor support writable enables for any supported counter
`sscsrind`     |                                                | Supervisor Indirect CSR access
`ssdbltrp`     |                                                | Supervisor Double Trap extension
`ssstateen`    |                                                | Supervisor State Enable extension
`ssnpm`        |                                                | Machine disable pointer masking for the nex
`ssqosid`      |                                                | Quality-of-Service (QoS) identifiers
`sstc`         |                                                | Supervisor-mode Timer interrupts
`sstvala`      |                                                | Supervisor `stval` provides all needed values
`sstvecd`      |                                                | Supervisor `stvec` supports direct mode
`ssupm`        |                                                | User-mode pointer masking
`ssu64xl`      |                                                | `UXLEN=64` must be supported
`svinval`      |                                                | Fine-grained Address-translation cache invalidation
`svadu`        |                                                | Hardware Updating of A/D bits
`svnapot`      |                                                | NAPOT Translation Contiguityt priviledge mode
`svade`        |                                                | Raise exception on improper A/D bits
`svbare`       |                                                | Bare mode virtual-memory translation supported
`svpbmt`       |                                                | Page-Based Memory Types
`sv32`         |                                                | Page-based 32-bit Virtual memory system
`sv39`         |                                                | Page-based 39-bit Virtual memory system
`sv48`         |                                                | Page-based 48-bit Virtual memory system
`sv57`         |                                                | Page-based 57-bit Virtual memory system
`svvptc`       |                                                | Obviating memory-managment instruction after marking PTEs valid
`zaamo`        |                                                | Atomic memory operations
`zabha`        | `zaamo`                                        | Byte and Halfword Atomic memory operations
`zacas`        | `zaamo`                                        | Atomic Compare-And-Swap (CAS) instructions
`zalrsc`       |                                                | Atomic load-reserve/store-conditional instructions
`zawrs`        |                                                | Atomic Wait-on-Reservation-Set instructions
`za64rs`       |                                                | Reservation set size of 64 bytes
`za128rs`      |                                                | Reservation set size of 128 bytes
`zba`          |                                                | Address generation
`zbb`          |                                                | Basic bit manipulation
`zbc`          |                                                | Carry-less multiplication
`zbkb`         |                                                | Bit manipulation for cryptography
`zbkc`         |                                                | Carry-less multiplication for cryptography
`zbkx`         |                                                | Crossbar permuations
`zbs`          |                                                | Single-bit instructions
`zca`          |                                                | Non-floating point compressed instructions
`zcb`          | `zca`                                          | Compressed simple operations (functionality dependent on other features)
`zcd`          | `zca`, `d`                                     | Compressed double-precision floating-point
`zcf`          | `zca`, `f`                                     | Compressed single-precision floating-point
`zclsd`        |                                                | Compressed Load/Store pair instructions for RV32
`zcmop`        | `Zca`                                          | Compressed May-Be-Operations
`zcmp`         | `zca`                                          | Compressed push/pop and double move (incompatible with `zcd`)
`zcmt`         | `zca`                                          | Compressed table jump instructions (incompatible with `zcd`)
`zfa`          | `f`                                            | Additional Floating-point instructions
`zfbfmin`      | `f`, `zfh`                                     | Scalar BF16 converts
`zfdinx`       | `zfinx`                                        | Double-precision Floating-point in INteger registers
`zfh`          | `zfhmin`                                       | Half-precision floating-point
`zfhinx`       | `zfinx`                                        | Half-precision Floating-point in INteger registers
`zfhinxmin`    | `zfinx`                                        | Minimal Half-precision Floating-point in INteger registers
`zfhmin`       | `f`                                            | Minimal Half-precision floating-point
`zfinx`        | `zficsr`                                       | Single-precision Floating-point in INteger registers
`ziccif`       |                                                | Main memory supports instruction fetch with atomicity requirement
`ziccamoa`     |                                                | Main memory supports all atomics in A
`zicclsm`      |                                                | Main memory supports misaligned loads/stores
`ziccrse`      |                                                | Main memory supports forward progress on LR/SC sequences
`zicbom`       |                                                | Cache-Block Managment
`zicbop`       |                                                | Cache-Block Prefetching
`zibhoz`       |                                                | Cache-Block Zeroing
`zicfilp`      | `zicsr`                                        | Control Flow Integrity (CFI) - Landing pad
`zicfiss`      |                                                | Control Flow Integrity (CFI) - Shadow Stack
`zicntr`       | `Zicsr`                                        | Counters - `CYCLE`, `TIME`, and `INSTRET`
`zicond`       |                                                | Integer Conditional operations
`zicsr`        |                                                | Control and status registers
`zic64b`       |                                                | Cache size is 64 bytes
`zifencei`     |                                                | Instruction-fetch fence
`zihintntl`    |                                                | Non-Temporarl Locatilty Hints
`zihintpause`  |                                                | Pause Hint
`zihpm`        | `Zicsr`                                        | Counters - Hardware PerforMance
`zilsd`        |                                                | Load/Store pair instructions for RV32
`zimop`        |                                                | May-Be-Operations
`zk`           | `zkn`, `zkr`, `zkt`                            | Standard scalar cryptography extensions
`zkn`          | `zbkb`, `zbkc`, `zbkx`, `zkne`, `zknd`, `zknh` | NIST algorithm suite
`zknd`         |                                                | NIST suite - AES decryption
`zkne`         |                                                | NIST suite - AES encryption
`zknh`         |                                                | NIST suite - Hash function instructions
`zkr`          |                                                | Entropy source extension
`zks`          | `zbkb`, `zbkc`, `zbkx`, `zksed`, `zksh`        | ShangMi algorith suite
`zksed`        |                                                | ShangMi suite - SM4 block cypher instructions
`zksh`         |                                                | ShangMi suite - SM3 block cypher instructions
`zkt`          |                                                | Data Independent Execution Latency
`zmmul`        |                                                | Integer multiplication
`ztso`         |                                                | Total Store Ordering
`zvbb`         | `zvb`                                          | Vector Basic Bit manipulation
`zvbc`         |                                                | Vector Carryless multiplication
`zve32f`       | `zve32x`                                       | Vector extension for embedded processors - minimum `vlen` of 32, fp32 support
`zve32x`       |                                                | Vector extension for embedded processors - minimum `vlen` of 32
`zve64d`       | `zve64f`                                       | Vector extension for embedded processors - minimum `vlen` of 64, fp64 support
`zve64f`       | `zve64x`, `zve32f`                             | Vector extension for embedded processors - minimum `vlen` of 64, fp32 support
`zve64x`       | `zve32x`                                       | Vector extension for embedded processors - minimum `vlen` of 64,
`zvfbfmin`     | `zve32f`                                       | Vector BF16 converts
`zvfbfwma`     | `zfbfmin`, `zvfbfwma`                          | Vector BF16 widening mul-add
`zvfh`         | `zvfhmin`                                      | Vector extension for Half-precision Floating point
`zvfhmin`      | `zve32f`                                       | Vector extension for Minimal Half-precision Floating-point
`zvkb`         |                                                | Vector Cryptography Bit manipulation
`zvkg`         |                                                | Vector Galois/Counter Node (GCM) and Galois Message Authentication Code (GMAC)
`zvkks`        | `zvksed`, `zvksh`, `zvkb`, `zvkt`              | ShangMi algorithm suite
`zvkn`         | `zvkned`, `zvknhb`, `zvkb`, `zvkt`             | NIST algorithm suite
`zvknc`        | `zvkn`, `zvbc`                                 | NIST algorithm suite with carryless multiplication
`zvknha`       |                                                | NIST suite - Vector SHA-2 secure hash - SHA-256 only
`zvknhb`       |                                                | NIST suite - Vector SHA-2 secure hash - SHA-256 and SHA-512
`zvkng`        | `zvkn`, `zvkg`                                 | NIST algorithm suite with Galois/Counter Mode (GCM)
`zvknhed`      |                                                | NIST suite - Vector AES block cypher
`zvkksc`       | `zvks`, `zvbc`                                 | ShangMi algorithm suite with carryless multiplication
`zvksed`       |                                                | ShangMi suite - Vector SM3 block cypher
`zvksg`        | `zvks`, `zvkg`                                 | ShangMi algorithm suite with Galois/Counter Mode (GCM)
`zvksh`        |                                                | ShangMi suite - Vector SM4 block cypher
`zvkt`         |                                                | Vector data-independent execution latency
`zvl*`         |                                                | Minimum vector length (`*` is one of: `32b`, `64b`, `128b`, `256b`, `512b`, `1024b`)

> _Note_: any instruction marked with a `z` can define a sub-instruction for the features implied by its second letter, i.e `zmmul` is an sub-instruction of the `m` features

> _Note_: In the future this table will be generated from compiler provided data