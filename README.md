
## apc inject
- [Snippets for detection of 32-bit/64-bit code segment](https://board.flatassembler.net/topic.php?t=20208)
- adapt the process execution environment through `\x31\xc0\x48\x0f\x88\x00\x00\x00\x00\xc3`.
- then automatically execute a different `LoadLibraryExW(32/64)`, lookup from PEB.
- generate shellcode, call apc injection. [shellcode design](./shellcode.md)

## thanks
- normal mode: [Blackbone](https://github.com/DarthTon/Blackbone)
- kernel mode: [Kernel-Bridge](https://github.com/HoShiMin/Kernel-Bridge)
- shellcode design: [flatassembler](https://flatassembler.net)