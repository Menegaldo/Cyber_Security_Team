# 2.out

###### Solved by @Cubano2  
> This is a CTF about [Temas abordados: análise de código, XOR]  

## About the Challenge  
Somos apresentados na questão apenar com um arquivo .out.

## Análise
Como de costume para esses desafios é sempre bom abrir em um descompilador, nesse exercício usei o Ghidra.
E o código desse arquivo possui essa ```main```

![image](https://github.com/user-attachments/assets/b975da75-32b2-4384-9bd4-f6b59875fbe1)

Ao analisar esse código podemos ver 2 funções que chamam a atenção.
As funções sendo: ```generate_password``` e ```encrypt_decrypt```
Ao ver que o ```local_48``` é enviado para essas 2 funções, imaginei que ele seria a senha, e na função ```encrypt_decrypt``` ele é enviado com o número em hexadecimal ```ffffffaa```.


## Solution
O modo em que resolvi essa questão foi diferente do modo como analisei, ao executar o código no terminal normalmente, ao colocar a senha errada você é recebido com um ```Access Denied!```.
Pórem no Ghidra percebi que a função ```generate_password``` executa antes da senha ser criptografada.
Com isso pensei que ao colocar o endereço de memória da função ```generate_password``` como breakpoint no GDB, já conseguiria ter acesso à senha criada.

![image](https://github.com/user-attachments/assets/d3822ddf-ee53-4036-83c3-d24a5ae394de)

Primeiramente achei que o endereço que chamava a ```generate_password``` fosse ```0x101235```
Mas não deu em nada então com o comando ```info functions``` no GDB tive acesso ao real endereço de memória que é: ```0x000055555555534d```.
Com isso defini o breakpoint e fui tentando achar a senha no pelo GDB no terminal.
```
(gdb) break *0x55555555534d
Breakpoint 2 at 0x55555555534d
(gdb) continue
The program is not being run.
(gdb) run
Starting program: /home/kali/Downloads/2.out 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 2, 0x000055555555534d in generate_password ()
(gdb) info registers
rax            0x7fffffffdcd0      140737488346320
rbx            0x7fffffffde28      140737488346664
rcx            0x555555557d90      93824992247184
rdx            0x555555556004      93824992239620
rsi            0x555555556004      93824992239620
rdi            0x7fffffffdcd0      140737488346320
rbp            0x7fffffffdd10      0x7fffffffdd10
rsp            0x7fffffffdc88      0x7fffffffdc88
r8             0x0                 0
r9             0x7ffff7fcbf40      140737353924416
r10            0x7fffffffda50      140737488345680
r11            0x202               514
r12            0x0                 0
r13            0x7fffffffde38      140737488346680
r14            0x7ffff7ffd000      140737354125312
r15            0x555555557d90      93824992247184
rip            0x55555555534d      0x55555555534d <generate_password>
eflags         0x246               [ PF ZF IF ]
cs             0x33                51
ss             0x2b                43
ds             0x0                 0
es             0x0                 0
fs             0x0                 0
gs             0x0                 0
fs_base        0x7ffff7db0740      140737351714624
gs_base        0x0                 0
(gdb) x/s 0x7fffffffdcd0
0x7fffffffdcd0: ""
(gdb) x/20x 0x7fffffffdcd0
0x7fffffffdcd0: 0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0x7fffffffdcd8: 0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0x7fffffffdce0: 0x00    0x00    0x00    0x00
(gdb) stepi
0x0000555555555351 in generate_password ()
(gdb) stepi
0x0000555555555352 in generate_password ()
(gdb) x/20x 0x7fffffffdcd0
0x7fffffffdcd0: 0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0x7fffffffdcd8: 0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0x7fffffffdce0: 0x00    0x00    0x00    0x00
(gdb) x/s 0x7fffffffdcd0
0x7fffffffdcd0: ""
(gdb) finish
Run till exit from #0  0x0000555555555352 in generate_password ()
0x000055555555523a in main ()
(gdb) x/s 0x7fffffffdcd0
0x7fffffffdcd0: "vh{|466:"
```

Com isso descobri que a senha gerada é ```vh{|466:```
E então executei o código pela última vez.
```
┌──(kali㉿kali)-[~/Downloads]
└─$ ./2.out      
Enter the password: vh{|466:
Access Granted!
```

>`Access Granted!`




