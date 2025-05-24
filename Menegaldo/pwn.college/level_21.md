```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, [rsp]
    add rax, [rsp + 8]
    add rax, [rsp + 16]
    add rax, [rsp + 24]
    shr rax, 2
    push rax
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{0PcfH330vfX2yGZHlMLDYRjW0An.dlDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/b550e724-e196-4373-a91f-d503ea3684e8)
