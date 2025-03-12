# 2.out

###### Solved by @Cubano2  
> This is a CTF about [Temas abordados: análise de código, XOR]  

## About the Challenge  
Somos apresentados na questão apenar com um arquivo .out.

## Análise
Como de costume para desafios desse tipo, o primeiro passo foi abrir o binário em um descompilador. Para essa análise, utilizei o Ghidra. Ao examinar o código, observei a seguinte função ```main```:

![image](https://github.com/user-attachments/assets/b975da75-32b2-4384-9bd4-f6b59875fbe1)

Duas funções chamaram minha atenção: ```generate_password``` e ```encrypt_decrypt```. O parâmetro ```local_48``` é passado para ambas, indicando que pode estar relacionado à senha. Além disso, notei que ```local_48``` é enviado para a função ```encrypt_decrypt``` com o valor hexadecimal ```0xffffffaa```.


## Solution
Ao executar o binário no terminal, percebi que uma senha incorreta resulta em ```Access Denied!```. No entanto, analisando no Ghidra, vi que a função ```generate_password``` é chamada antes do processo de criptografia da senha.

Dado isso, minha abordagem foi definir um breakpoint no endereço da função ```generate_password``` dentro do GDB, permitindo inspecionar a senha gerada antes de ser utilizada.

![image](https://github.com/user-attachments/assets/d3822ddf-ee53-4036-83c3-d24a5ae394de)

Inicialmente, tentei definir um breakpoint em ```0x101235```, mas não obtive sucesso. Utilizando ```info functions``` no GDB, descobri que o endereço real da função era ```0x000055555555534d```.
Defini o breakpoint correto e iniciei o programa com ```run```, permitindo a interrupção na função ```generate_password```:
```
(gdb) break *0x55555555534d
Breakpoint 2 at 0x55555555534d
(gdb) run
```
Quando o breakpoint foi acionado, utilizei ```info registers``` para identificar os valores dos registradores e ```x/s``` para visualizar o conteúdo da memória:
```
(gdb) x/s 0x7fffffffdcd0
0x7fffffffdcd0: ""
(gdb) stepi  # Avancei algumas instruções
(gdb) x/s 0x7fffffffdcd0
0x7fffffffdcd0: "vh{|466:"
```
Assim, descobri que a senha gerada era ```vh{|466:```.
Por fim, executei o binário novamente e inseri a senha:
```
┌──(kali㉿kali)-[~/Downloads]
└─$ ./2.out      
Enter the password: vh{|466:
Access Granted!
```

>`Access Granted!`




