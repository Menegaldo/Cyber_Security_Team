```py
from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov [0x404000], rax
""")

print(shellcode)

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{ITXH3peQ0UdKO-9MRct9bNcz8rN.QXzEDOzwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/4e283407-fe70-48d4-9b21-877cdcd69380)
