```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    and rdi, 1
    xor rdi, 1
    xor rax, rax
    or rax, rdi
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{YyPXT9JgpYsuJ8F9jMtyHL0vPq8.dJDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/5166a994-44de-4943-9b72-621402bff7a0)
