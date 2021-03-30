# RISC-V Working Groups: Fast-Interrupts

The Core-Local Interrupt Controller (CLIC) is designed to provide low-latency, vectored, pre-emptive interrupts for RISC-V systems. When activated the CLIC subsumes and replaces the original RISC-V basic local interrupt scheme. The CLIC has a base design that requires minimal hardware, but supports additional extensions to provide hardware acceleration. The goal of the CLIC is to provide support for a variety of software ABI and interrupt models, without complex hardware that can impact high-performance processor implementations.

link to documentation
https://github.com/riscv/riscv-fast-interrupt
