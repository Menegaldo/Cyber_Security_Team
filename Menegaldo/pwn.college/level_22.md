```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, 0x403000
    jmp rax
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{AYVRnIdRgoQfbqMh-kpuuwRpwky.QX1EDOzwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/6dcf922f-0d25-4b32-bba5-524a0ccdce0e)
