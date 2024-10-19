# flatparsebug

This reproduces a bug in compiling `flatparse` with `-fllvm` on Windows.

How to create this repository:

Do this:
```
PS> stack new flatparsebug
PS> cd flatparsebug
```

Then add `flatparse` to `package.yaml`:

```yaml
dependencies:
- flatparse == 0.5.1.0
```

And finally, try to build:

```
PS> stack build
```

Error:
```
flatparse   > [15 of 25] Compiling FlatParse.Examples.BasicLambda.Lexer
flatparse   > ghc-9.6.6.exe: internal error: IMAGE_REL_AMD64_ADDR32[NB]: High bits are set in 0x7ff62bd32368 for ghczmprim_GHCziPrimziPanic_absentSumFieldError_closure
flatparse   >     (GHC version 9.6.6 for x86_64_unknown_mingw32)
flatparse   >     Please report this as a GHC bug:  https://www.haskell.org/ghc/reportabug
Progress 1/2

Error: [S-7282]
       Stack failed to execute the build plan.
```

My LLVM version:
```
PS> opt --version
LLVM (http://llvm.org/):
  LLVM version 15.0.7
  Optimized build.
  Default target: x86_64-pc-windows-msvc
  Host CPU: rocketlake

PS> llc --version
LLVM (http://llvm.org/):
  LLVM version 15.0.7
  Optimized build.
  Default target: x86_64-pc-windows-msvc
  Host CPU: rocketlake

  Registered Targets:
    aarch64    - AArch64 (little endian)
    aarch64_32 - AArch64 (little endian ILP32)
    aarch64_be - AArch64 (big endian)
    amdgcn     - AMD GCN GPUs
    arm        - ARM
    arm64      - ARM64 (little endian)
    arm64_32   - ARM64 (little endian ILP32)
    armeb      - ARM (big endian)
    avr        - Atmel AVR Microcontroller
    bpf        - BPF (host endian)
    bpfeb      - BPF (big endian)
    bpfel      - BPF (little endian)
    hexagon    - Hexagon
    lanai      - Lanai
    mips       - MIPS (32-bit big endian)
    mips64     - MIPS (64-bit big endian)
    mips64el   - MIPS (64-bit little endian)
    mipsel     - MIPS (32-bit little endian)
    msp430     - MSP430 [experimental]
    nvptx      - NVIDIA PTX 32-bit
    nvptx64    - NVIDIA PTX 64-bit
    ppc32      - PowerPC 32
    ppc32le    - PowerPC 32 LE
    ppc64      - PowerPC 64
    ppc64le    - PowerPC 64 LE
    r600       - AMD GPUs HD2XXX-HD6XXX
    riscv32    - 32-bit RISC-V
    riscv64    - 64-bit RISC-V
    sparc      - Sparc
    sparcel    - Sparc LE
    sparcv9    - Sparc V9
    systemz    - SystemZ
    thumb      - Thumb
    thumbeb    - Thumb (big endian)
    ve         - VE
    wasm32     - WebAssembly 32-bit
    wasm64     - WebAssembly 64-bit
    x86        - 32-bit X86: Pentium-Pro and above
    x86-64     - 64-bit X86: EM64T and AMD64
    xcore      - XCore
```

LLVM installation:

I installed LLVM by compiling it from source:
```
PS> git clone https://github.com/llvm/llvm-project.git
PS> git checkout release/15.x
PS> cd llvm-project
PS> mkdir build
PS> cmake -DCMAKE_BUILD_TYPE=Release [etc.]
PS> cd build
PS> ninja install
(Then add to PATH)
```

Remarks:
- Without `-fllvm`, the build completes successfully, regardless of
  the optimization level.
- In contrast, with `-fllvm`, the build always fails with this error,
  regardless of the optimization level.
- As of this writing, GHC 9.6.6 is offered as part of the latest stable
  Stackage LTS.
