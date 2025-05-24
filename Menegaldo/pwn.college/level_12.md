```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
  mov rax, [0x404000]
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{YYAf5N_-PacPqfMfyNWn-xPZJ7n.QXyEDOzwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/f07170f5-9438-44a2-8dab-daa09f4be9f8)
