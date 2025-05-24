```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, rdi
    xor rdx, rdx
    div rsi
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{IhcPKTmHsns-XismSTaxN5ZGCov.ddTOxwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/9b0d1f1d-b350-4c62-be9c-285cbb502003)
