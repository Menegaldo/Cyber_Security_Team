```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    add rdi, 0x331337
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{8n8euzSEeP9Y6Xo2dN7G6pe1Aik.dVTOxwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/8be2f53b-b032-46e0-a020-5bfe192bf540)
