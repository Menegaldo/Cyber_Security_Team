```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, rdi
    xor rdx, rdx
    div rsi
    mov rax, rdx
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{853HQUN-mDS5GzmO9UeN3BiioIB.dhTOxwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/2ea5f4fa-48cf-420d-ab43-47e9dc7fcd01)
