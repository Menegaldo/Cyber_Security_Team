# Phantom Processing Platform

###### Solved by @[Seu usuário]  
> This is a CTF about [Temas abordados: Buffer Overflow, Race Condition etc]  

## About the Challenge  

Reporter.<br>
kylar Preliminary Investigator.<br>
babaisflag Type.<br>
Spectral Analysis Description.<br>

The Phantom Processing Platform stands as a beacon for the fearless, a digital haven where ghost sightings are not just reported but embraced and analyzed. Courageous souls gather here, their findings unveiling the mysteries that whisper from the shadows. It's a testament to human curiosity, daring to unveil what lurks in the unseen Hypotheses. Phantoms! Run or observe?

// <!-- Tradução

Plataforma de processamento fantasma

Repórter.<br>
kylar Investigador Preliminar.<br>
babaisflag Tipo.<br>
Análise Espectral Descrição.<br>

A Plataforma de Processamento Fantasma se destaca como um farol para os destemidos, um refúgio digital onde avistamentos de fantasmas não são apenas relatados, mas acolhidos e analisados. Almas corajosas se reúnem aqui, suas descobertas revelando os mistérios que sussurram das sombras. É um testamento à curiosidade humana, ousando revelar o que se esconde nas Hipóteses invisíveis. Fantasmas! Correr ou observar?

--> Tradução

## Solution  

### Verificar os arquivos fornecidos

![image](https://github.com/user-attachments/assets/781ff8f6-5b8e-40f5-8ab7-0413bd73913f)

---

| Arquivo         | Função Principal             | O que fazer com ele                                                                 |
|-----------------|------------------------------|--------------------------------------------------------------------------------------|
| **`Dockerfile`** | Ambiente de execução (opcional) | Emula o ambiente usado pelo autor (QEMU + deps). Útil se tiver problemas locais.     |
| **`rootfs.cpio`** | Sistema de arquivos            | Contém os binários e arquivos que serão carregados no boot. Onde mora o binário vulnerável. |
| **`zImage`**     | Imagem do kernel Linux         | Boota primeiro no QEMU. Monta o rootfs. Só investigar se suspeitar de bug no kernel. |
| **`run_qemu.sh`**| Script de inicialização        | Executa o QEMU com os argumentos corretos. Essencial para entender como o sistema sobe. |

---

Converter o run_qemu.sh para rodar:
```
chmod +x run_qemu.sh
```

nc localhost 1337 da esse menu:
```
Welcome to the Phantom Processing Platform

⠀⠀⠀⠀⠀⠀⢀⣤⣶⣶⣖⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⢀⣾⡟⣉⣽⣿⢿⡿⣿⣿⣆⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⢠⣿⣿⣿⡗⠋⠙⡿⣷⢌⣿⣿⠀⠀⠀⠀⠀⠀⠀
⣷⣄⣀⣿⣿⣿⣿⣷⣦⣤⣾⣿⣿⣿⡿⠀⠀⠀⠀⠀⠀⠀
⠈⠙⠛⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣧⡀⠀⢀⠀⠀⠀⠀
⠀⠀⠀⠸⣿⣿⣿⣿⣿⣿⣿⣿⣿⡟⠻⠿⠿⠋⠀⠀⠀⠀
⠀⠀⠀⠀⠹⣿⣿⣿⣿⣿⣿⣿⣿⡇⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠈⢿⣿⣿⣿⣿⣿⣿⣇⠀⠀⠀⠀⠀⠀⠀⡄
⠀⠀⠀⠀⠀⠀⠀⠙⢿⣿⣿⣿⣿⣿⣆⠀⠀⠀⠀⢀⡾⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⣿⣿⣿⣿⣷⣶⣴⣾⠏⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠉⠛⠛⠛⠋⠁⠀⠀⠀

Ghost Data Management System
1. Add Ghost Data               // Adicionar dados fantasmas
2. Show Ghost Data              // Mostrar dados fantasmas
3. Edit Ghost Data              // Editar dados fantasmas
4. Delete Ghost Data            // Excluir dados fantasmas
5. Analyze Ghost Data           // Analisar dados fantasmas
6. Switch Mode                  // Alternar modo
7. Exit                         // Sair

Enter your choice:              // Faça sua escolha
```

Tentando acessar o binario que está rodando no netcat:
```
mkdir rootfs && cd rootfs
cp ../rootfs.cpio .
cpio -idmv < rootfs.cpio
```

resultado:
```
┌──(kali㉿kali)-[~/Downloads/Phantom_Processing_Platform/distrib/rootfs]
└─$ ls
bin                   proc
dev                   processor_arm
etc                   root
flag                  rootfs.cpio
handle_connection.sh  run
init                  sbin
lib                   sensor_arm
lib32                 start.sh
linuxrc               sys
media                 tmp
mnt                   usr
opt                   var

```

Procurando pela string "Welcome to the Phantom Processing Platform": 
```
┌──(kali㉿kali)-[~/Downloads/Phantom_Processing_Platform/distrib/rootfs]
└─$ grepgrep -r "Welcome to the Phantom Processing Platform" .

grep: ./sensor_arm: binary file matches
grep: ./rootfs.cpio: binary file matches // compactado orginal
```

Procurar pela string `Ghost` com strings sensor_arm | grep Ghost

![image](https://github.com/user-attachments/assets/60dab0fc-0872-47f5-9bb1-108f36a9ca25)

Procurar pela string `Phantom` com strings sensor_arm | grep Phantom

![image](https://github.com/user-attachments/assets/832438e8-ca27-47ca-a3f0-32730a33550f)

Executavel localizado, é o `sensor_arm`, vamos abrir no ghidra e ver o que achamos:

```c
undefined4 main(int param_1,undefined4 *param_2)

{
  int local_c;
  
  if (param_1 != 2) {
    fprintf(stderr,"Usage: %s <socket_path>\n",*param_2);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  SOCKET_PATH = param_2[1];
  initFunction();
  setup_socket();
  puts("Welcome to the Phantom Processing Platform");
  printf("\n%s",getGhostArt);
  do {
    showMenu();
    __isoc99_scanf(&DAT_00012e70,&local_c);
    getchar();
    switch(local_c) {
    case 1:
      readGhostData();
      break;
    case 2:
      showGhostData();
      break;
    case 3:
      editGhostData();
      break;
    case 4:
      deleteGhostData();
      break;
    case 5:
      analyzeGhostData();
      break;
    case 6:
      switch_classic_modern();
      break;
    case 7:
      puts("Exiting.");
      close_socket(sock_fd);
      return 0;
    default:
      puts("Invalid choice. Please enter a number between 1 and 7.");
    }
  } while( true );
}
```

Olhado para a main() vemos que ela recebe um parametro de fora, vindo do `handle_connection.sh`, o que tem dentro dele.

```c
#!/bin/bash

socket_path=$(mktemp /tmp/sockets/socket_XXXXXX)
// Cria um caminho de socket temporário, como /tmp/sockets/socket_abcd12

cleanup() {
    sleep 1
    kill -TERM -$$  2>/dev/null
    rm -f "$socket_path"  
}
// Quando o script termina ou o tempo estoura, ele mata o processo atual (com kill -TERM -$$) e remove o socket temporário.

enforce_timeout() {
    local timeout_seconds=20
    sleep $timeout_seconds
    cleanup
    kill -TERM -$$  
}
// Força um timeout de 20 segundos pro processo. Depois disso, o programa é terminado automaticamente.

trap cleanup EXIT

enforce_timeout &

/processor_arm "$socket_path" > /dev/null 2>&1 &
// Executa processor_arm (em segundo plano), passando o caminho do socket.

sleep 0.1  

/sensor_arm "$socket_path"
// Executa o nosso alvo, sensor_arm, e passa o mesmo socket path.
```

- sensor_arm se comunica com processor_arm por meio de um socket UNIX.
- Pode haver troca de mensagens, comandos ou validação entre os dois.


vamos analisar a `readGhostData()`

```c
void readGhostData(void)

{
  int iVar1;
  undefined auStack_144 [310];
  ushort local_e;
  char *local_c;
  
  createGhostPacket(auStack_144);
  iVar1 = readPacket(auStack_144);
  if (iVar1 == 0) {
    sendCmd(sock_fd,&DAT_00012b9c);
    sendGhostPacket(sock_fd,auStack_144);
    sendEnd(sock_fd);
    local_c = (char *)readCmd(sock_fd);
    if ((local_c == (char *)0x0) || (iVar1 = strcmp(local_c,"SUC"), iVar1 != 0)) {
      puts("Failed to add ghost data.");
    }
    else {
      local_e = readId(sock_fd);
      printf("Successfully added ghost data with ID %hu.\n",(uint)local_e);
      readEnd(sock_fd);
    }
  }
  else {
    puts("Failed to add ghost data.");
  }
  return;
}
```

auStack_144 [310] é um buffer local grande na stack
→ Vulnerável se:

createGhostPacket ou readPacket não limitarem o tamanho

Ou se algum campo for copiado sem verificação

Se tiver um strcpy ou memcpy mal usado dentro de createGhostPacket() ou readPacket(), temos BOF na stack.

```c
void * createGhostPacket(void *param_1)

{
  undefined auStack_138 [12];
  undefined auStack_12c [36];
  undefined auStack_108 [256];
  
  memset(auStack_12c,0,0x20);
  memset(auStack_108,0,0x100);
  memcpy(param_1,auStack_138,0x130);
  return param_1;
}
```

Não tem nada em createGhostPacket().

```
undefined4 readPacket(uint *param_1)

{
  int iVar1;
  uint uVar2;
  uint uVar3;
  float fVar4;
  undefined auStack_34 [35];
  char local_11;
  
  puts("Enter title of your ghost observation report: ");
  iVar1 = readInput(param_1 + 3,0x18);
  if (iVar1 < 1) {
    puts("Failed to read title.");
  }
  else {
    puts("Enter timestamp of your ghost observation (unix epoch): ");
    __isoc99_scanf(&DAT_00012928,param_1);
    uVar3 = *param_1;
    uVar2 = time((time_t *)0x0);
    if (uVar2 < uVar3) {
      puts("Invalid timestamp.");
    }
    else {
      puts("Enter latitude and longitude of your ghost observation: ");
      __isoc99_scanf("%f %f",param_1 + 1,param_1 + 2);
      if ((-1 < (int)((uint)((float)param_1[1] < -90.0) << 0x1f)) &&
         (fVar4 = (float)param_1[1], fVar4 == 90.0 || fVar4 < 90.0 != NAN(fVar4))) {
        if (((int)((uint)((float)param_1[2] < -180.0) << 0x1f) < 0) ||
           (fVar4 = (float)param_1[2], fVar4 != 180.0 && fVar4 < 180.0 == NAN(fVar4))) {
          puts("Invalid longitude. It must be between -180 and 180.");
          return 0xffffffff;
        }
        puts("Enter confidence level of your ghost observation (0-100): ");
        __isoc99_scanf(&DAT_00012a28,(int)param_1 + 0x2d);
        if ((*(char *)((int)param_1 + 0x2d) < '\0') || ('d' < *(char *)((int)param_1 + 0x2d))) {
          puts("Invalid confidence level.");
          return 0xffffffff;
        }
        getchar();
        puts(
            "Enter ghost type (poltergeist, wraith, shadow_figure, revenant, spectre, doppelganger, custom_ghost): "
            );
        iVar1 = readInput(auStack_34,0x20);
        if (iVar1 < 1) {
          puts("Failed to read ghost type.");
          return 0xffffffff;
        }
        local_11 = stringToGhostType(auStack_34);
        if (local_11 == -1) {
          puts("Invalid ghost type.");
          return 0xffffffff;
        }
        *(char *)(param_1 + 0xb) = local_11;
        assignDescriptionToPacket(param_1);
        return 0;
      }
      puts("Invalid latitude. It must be between -90 and 90.");
    }
  }
  return 0xffffffff;
}
```

readInput(auStack_34, 0x20) lê a string do tipo do fantasma
Se readInput não limita corretamente o tamanho ou não adiciona NUL-terminator, isso pode causar:

Overflow de auStack_34[35] (pois buffer local tem 35 bytes e lê até 32 = ok, mas depende da implementação de readInput)

Mas isso ainda está na stack da função, então não afeta diretamente param_1.

---------------

Analisar assignDescriptionToPacket()

Isso é a parte mais arriscada. Por quê?

Não sabemos ainda como o "description" é lido

Pode estar usando gets, strcpy, strcat, ou mesmo read() sem checagem

Se ele escreve dentro de param_1, é possível que escreva além dos 0x130 bytes do buffer

```c
void assignDescriptionToPacket(int param_1)

{
  if ((*(char *)(param_1 + 0x2c) < '\0') || ('\a' < *(char *)(param_1 + 0x2c))) {
    assignCustomGhostDescriptionToPacket(param_1);
  }
  else {
    assignDefaultGhostDescriptionToPacket(param_1);
  }
  return;
}


ssize_t readInput(undefined *param_1,int param_2)

{
  ssize_t sVar1;
  
  sVar1 = read(0,param_1,param_2 - 1);
  if (-1 < sVar1) {
    if (sVar1 == 0) {
      *param_1 = 0;
      sVar1 = 0;
    }
    else if (param_1[sVar1 + -1] == '\n') {
      param_1[sVar1 + -1] = 0;
    }
    else {
      param_1[sVar1] = 0;
    }
    return sVar1;
  }
                    /* WARNING: Subroutine does not return */
  exit(1);
}
```

Analisando a assignCustomGhostDescriptionToPacket()

```c
void assignCustomGhostDescriptionToPacket(int param_1)

{
  printf("Enter description for the custom ghost: ");
  readInput(param_1 + 0x30,0x100);
  return;
}
```

Lê 0xFF (255) bytes do usuário via readInput (lembrando: readInput() lê até size-1 e bota um \0)

Escreve no offset param_1 + 0x30 (ou seja, deslocamento de 0x30 dentro do buffer param_1)

------------------

Na readGhostData():

undefined auStack_144[310];  // 0x136 bytes
Mas… param_1 + 0x30 → offset de 48 bytes.

Se você escreve 0xFF bytes a partir do offset 0x30, está escrevendo até:

0x30 + 0xFF = 0x12F
Ou seja, escreve até o byte 0x12F dentro do buffer de 0x136.

Isso parece ok à primeira vista, mas atenção: o stack frame pode conter outras variáveis após esse buffer (como local_e ou local_c) e principalmente o canary ou endereço de retorno, dependendo do alinhamento.




>`[Insira a flag]`
