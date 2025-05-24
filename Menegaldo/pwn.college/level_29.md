```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    xor rax, rax
    test rdi, rdi
    je done

    mov r8, rdi

loop:
    mov bl, byte ptr [r8]
    test bl, bl
    je done

    cmp bl, 0x5a
    ja skip

    mov rsi, r8
    movzx rdi, bl
    mov rcx, rax
    mov rax, 0x403000
    call rax
    mov byte ptr [rsi], al
    mov rax, rcx
    inc rax

skip:
    inc r8
    jmp loop

done:
    ret
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{ERwz5NlBrxf6vsngOK5Qljv7oKn.dVTMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/8788517c-20b9-4e04-98d7-fc3cfcfacac6)

-----

