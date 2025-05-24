```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, [rdi]
    add rax, [rdi + 8]
    mov [rsi], rax
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{4mKK7lqvWcV78gDCT1glwV8VmKU.dZDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/f36445df-b593-4a38-9a77-21e22f9c8855)
