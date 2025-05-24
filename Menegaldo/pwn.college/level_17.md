```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, 0xdeadbeef00001337
    mov [rdi], rax

    mov rbx, 0xc0ffee0000
    mov [rsi], rbx
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{0WOMKYhNAUiA8NxWk1qCZrv4VkV.dVDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/09e49df3-aa1a-4601-a867-4c1d2a2bbc43)
