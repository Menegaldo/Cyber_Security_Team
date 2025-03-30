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





>`[Insira a flag]`
