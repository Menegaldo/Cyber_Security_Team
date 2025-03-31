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



