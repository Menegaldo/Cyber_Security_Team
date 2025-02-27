
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
Nessa função ```init_ABCDEFG``` não entendi muito sua função, mas olhando com calma percebi que no final dela havia uma soma de todos os horcruxes:
```sum = g + a + b + c + d + e + f;```

>`[Insira a flag]`

