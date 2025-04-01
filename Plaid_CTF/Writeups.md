
caminho/…/distrib/cpio_contents/etc/init.d

S99start
```sh
otimo, vamos continuar, consegui acessar 

#!/bin/sh

# Script name: S99start

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

stop() {
    echo ""
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart|reload)
        stop
        start
        ;;
    *)
        echo "Usage: $0 {start|stop|restart|reload}"
        exit 1
esac

exit $?
```

--------------------------
Enter your choice:
```
6
```
Do you want to switch to modern mode? (y/n):
```
y
```

Enter your choice:
```
1
```
             
Enter title of your ghost observation report: 
```
AAAAAAAAAAAAAAAAAAAAA
```

← exatamente 21 letras (sem quebras de linha)

Enter timestamp of your ghost observation (unix epoch): 
```
1711970000
```

Enter latitude and longitude of your ghost observation: 
```
0.0
```
```
0.0
```

Enter confidence level of your ghost observation (0-100): 
```
1
```
← precisa ser ≤ 5

Enter ghost type (poltergeist, wraith, shadow_figure, revenant, spectre, doppelganger, custom_ghost): 
```
custom_ghost
```

Enter description for the custom ghost: 
```
teste
```

Enter your choice:
```
2
```

Enter the index of the ghost data to show:
```
1
```
--------------------

```py
void appendEmoji(int param_1)

{
  size_t sVar1;
  undefined4 *puVar2;
  
  if (is_modern == 0) {
    if (*(char *)(param_1 + 0x2d) < '`') {
      if (*(char *)(param_1 + 0x2d) < 'B') {
        if (*(char *)(param_1 + 0x2d) < '$') {
          if (*(char *)(param_1 + 0x2d) < '\x06') {
            sVar1 = strlen((char *)(param_1 + 0xc));
            *(undefined4 *)((char *)(param_1 + 0xc) + sVar1) = 0x2d2d20;
          }
          else {
            sVar1 = strlen((char *)(param_1 + 0xc));
            *(undefined4 *)((char *)(param_1 + 0xc) + sVar1) = 0x2d2020;
          }
        }
        else {
          sVar1 = strlen((char *)(param_1 + 0xc));
          *(undefined4 *)((char *)(param_1 + 0xc) + sVar1) = 0x7e2020;
        }
      }
      else {
        sVar1 = strlen((char *)(param_1 + 0xc));
        *(undefined4 *)((char *)(param_1 + 0xc) + sVar1) = 0x2b2020;
      }
    }
    else {
      sVar1 = strlen((char *)(param_1 + 0xc));
      *(undefined4 *)((char *)(param_1 + 0xc) + sVar1) = 0x2b2b20;
    }
  }
  else if (*(char *)(param_1 + 0x2d) < '`') {
    if (*(char *)(param_1 + 0x2d) < 'B') {
      if (*(char *)(param_1 + 0x2d) < '$') {
        if (*(char *)(param_1 + 0x2d) < '\x06') {
          sVar1 = strlen((char *)(param_1 + 0xc));
          puVar2 = (undefined4 *)((char *)(param_1 + 0xc) + sVar1);
          *puVar2 = 0xefb998e2;
          puVar2[1] = 0x9ff08fb8;
          *(undefined2 *)(puVar2 + 2) = 0xbb91;
          *(undefined *)((int)puVar2 + 10) = 0;
        }
        else {
          sVar1 = strlen((char *)(param_1 + 0xc));
          puVar2 = (undefined4 *)((char *)(param_1 + 0xc) + sVar1);
          *puVar2 = 0x95989ff0;
          puVar2[1] = 0xbb919ff0;
          *(undefined *)(puVar2 + 2) = 0;
        }
      }
      else {
        sVar1 = strlen((char *)(param_1 + 0xc));
        puVar2 = (undefined4 *)((char *)(param_1 + 0xc) + sVar1);
        *puVar2 = 0x90989ff0;
        puVar2[1] = 0xbb919ff0;
        *(undefined *)(puVar2 + 2) = 0;
      }
    }
    else {
      sVar1 = strlen((char *)(param_1 + 0xc));
      puVar2 = (undefined4 *)((char *)(param_1 + 0xc) + sVar1);
      *puVar2 = 0x94a49ff0;
      puVar2[1] = 0xbb919ff0;
      *(undefined *)(puVar2 + 2) = 0;
    }
  }
  else {
    sVar1 = strlen((char *)(param_1 + 0xc));
    puVar2 = (undefined4 *)((char *)(param_1 + 0xc) + sVar1);
    *puVar2 = 0x81989ff0;
    puVar2[1] = 0xbb919ff0;
    *(undefined *)(puVar2 + 2) = 0;
  }
  return;
}
```
