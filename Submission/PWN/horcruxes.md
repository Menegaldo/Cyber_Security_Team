
# Horcruxes
###### Solved by @Cubano2  


## About the Challenge  
Voldemort concealed his splitted soul inside 7 horcruxes.
Find all horcruxes, and ROP it!
author: jiwon choi

## Solution  

A questão começa logando nesse ssh ```ssh horcruxes@pwnable.kr -p2222``` e dentro dele temos acesso a 2 arquivos:
```horcruxes``` e ```readme```.

Colocando no terminal ```cat readme``` podemos ler o arquivo, dentro dele contém essa mensagem:

` horcruxes@pwnable:~$ cat readme
connect to port 9032 (nc 0 9032). the 'horcruxes' binary will be executed under horcruxes_pwn privilege.
rop it to read the flag. `


>`[Insira a flag]`

