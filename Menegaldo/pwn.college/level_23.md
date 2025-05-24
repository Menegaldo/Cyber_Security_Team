```py
from pwn import asm, context, process

context.update(arch='amd64', os='linux')

shellcode = asm("""
    jmp skip
    .rept 0x51
    nop
    .endr
skip:
    mov rax, 0x1
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{kvo-jwlHVCh-l8iHVJggjjjWrde.QX2EDOzwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/5f8c4054-8e76-4367-bad8-e95278039c2d)
