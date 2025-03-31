Documentos Fornecidos:

Dockerfile   rootfs.cpio   run_qemu.sh   zImage

**run_qemu.sh** = Executável que inicia a VM, roda a imagem (**zImage**) do kernel e o **rootfs.cpio** dentro.

```sh
qemu-system-arm \                                    # Inicia o QEMU emulando uma máquina ARM
    -smp cores=4 \                                   # Usa 4 núcleos de CPU.
    -M virt \                                        # Modelo de máquina virtual genérico (ARM "virt").
    -m 4096 \                                        # Aloca 4GB de RAM para a VM.
    -monitor /dev/null \                             # Descarta a saída do monitor do QEMU.
    -kernel ./zImage \                               # Carrega o kernel Linux (arquivo 'zImage').
    -initrd ./rootfs.cpio \                          # Carrega o sistema de arquivos inicial (RAM disk).
    -append "console=ttyAMA0 root=/dev/ram" \        # Configura o console e o disco raiz.
    -netdev user,id=vmnic,hostfwd=tcp::1337-:1337 \  # Redireciona a porta 1337 (host -> VM).
    -device virtio-net-device,netdev=vmnic \         # Adiciona uma placa de rede virtual.
    -nographic                                       # Roda sem interface gráfica (tudo no terminal).
```

------------------------------------------------------------------------------------------------------------------------------

rodando o run_qemu.sh temos pedido de login e senha no terminal.

```
Phantom login: teste
Password: 
```

E liberação da porta 1337 para acesso, como o único arquivo que temos de instrução é o rootfs.cpio, deve ser o que está na interface netcat.

```
nc localhost 1337  
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
1. Add Ghost Data
2. Show Ghost Data
3. Edit Ghost Data
4. Delete Ghost Data
5. Analyze Ghost Data
6. Switch Mode
7. Exit

Enter your choice:
```

visto que temos um menu interativo, podemos tentar extrair as informações do rootfs.cpio para analisarmos melhor, para isso usamos:

```
mkdir rootfs && cd rootfs
cp ../rootfs.cpio .
cpio -idmv < rootfs.cpio
```

Agora temos os seguintes arquivos:

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

Os arquivos que chamam atenção de cara são:

Esses dois são arquivos de inicialização:
```
handle_connection.sh
start.sh
```

 O `handle_connection.sh` cria um socket temporário e inicializa o `/processor_arm` e o `/sensor_arm`.


