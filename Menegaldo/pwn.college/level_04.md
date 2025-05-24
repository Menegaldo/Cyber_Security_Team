```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, rdi
    imul rax, rsi
    add rax, rdx
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{c-0quxHrlihLpT9iBT_F39hv7GR.dZTOxwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/50ae2298-fdf7-46ef-b710-147d60fff832)
