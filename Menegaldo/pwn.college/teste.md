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

----

```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    test rdi, rdi
    je done

    xor rax, rax
loop:
    mov bl, byte ptr [rdi + rax]
    test bl, bl
    je done
    inc rax
    jmp loop

done:
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{8XYnZZtIJDFTGTR_NzBILcCTb8T.dRTMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/ea769a44-c551-4f3a-bb77-5d2b567b8e77)

-------
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

