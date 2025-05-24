```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

# Assembly sem comentários inline (para evitar erro de sintaxe)
shellcode = asm("""
    xor rax, rax
    xor rcx, rcx

loop:
    mov rbx, [rdi + rcx*8]
    add rax, rbx
    inc rcx
    cmp rcx, rsi
    jne loop

    xor rdx, rdx
    div rsi
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{0AbEt3iCT-4zKu1bMSz1hiRHRhD.dNTMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/2992448c-798b-4dce-ab9a-47ca455ea85e)
