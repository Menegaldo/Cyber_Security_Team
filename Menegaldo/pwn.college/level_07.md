```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
  mov ah, 0x42
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{kUgrnYXJBzqr9OMfOcBTk4vLYvI.QXxEDOzwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/bfc3c45a-6b71-442c-98c2-06a1c5ddaeb7)

