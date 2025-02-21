# Eavesdrop
###### Resolvido por @Cubano2
> Este é um CTF sobre [ Capture of Files, Eavesdrop Messages, Forensic Data Extraction]  

## Sobre o Desafio  

Baixe esta captura de pacote e encontre o sinalizador.

## 🔗 Links do Desafio
[Eavesdrop](https://play.picoctf.org/practice/challenge/264) <br>
[Arquivo](https://artifacts.picoctf.net/c/134/capture.flag.pcap) <br>

### Análise Inicial
O desafio começa com o download do arquivo ```.pcap```, que pode ser analisado com o Wireshark. A dica fornecida pelo próprio CTF é:
```"Tudo o que sabemos é que esta captura de pacotes inclui uma conversa de chat e uma transferência de arquivo."```

Ao abrirmos o arquivo no Wireshark, nos deparamos com pacotes de protocolos variados.
![image](https://github.com/user-attachments/assets/47371886-e51a-4e75-ba2d-fae2e82ac28f)

## 🛠️ Solução  
O Wireshark é uma ferramenta poderosa para análise de tráfego de rede, permitindo filtrar pacotes específicos e visualizar o conteúdo transmitido, caso a conexão não seja segura, como no protocolo HTTP.

A partir da dica do desafio, sabemos que devemos encontrar uma conversa de chat e uma transferência de arquivo. Para isso, aplicamos o seguinte filtro no Wireshark: ```tcp && data```
![image](https://github.com/user-attachments/assets/1c7dc7c3-3448-457a-aec6-7f93e25a4947)

Esse filtro nos permite visualizar pacotes TCP que contêm dados.

![image](https://github.com/user-attachments/assets/66027c1a-4542-4512-bf9c-bc4f073e998b)


Ao analisar os pacotes filtrados, identificamos uma conversa entre duas pessoas. Para visualizar a conversa no Wireshark, seguimos o caminho:

Analyze → Follow → TCP Stream

A conversa interceptada revelou o seguinte conteúdo:
```
Hey, how do you decrypt this file again?
You're serious?
Yeah, I'm serious
*sigh* openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123
Ok, great, thanks.
Let's use Discord next time, it's more secure.
C'mon, no one knows we use this program like this!
Whatever.
Hey.
Yeah?
Could you transfer the file to me again?
Oh great. Ok, over 9002?
Yeah, listening.
Sent it
Got it.
You're unbelievable
```

Com base nessa conversa, percebemos que a transferência de arquivo ocorreu na porta 9002. Aplicamos então o seguinte filtro para isolar esses pacotes:
```tcp.port == 9002```
Isso nos permitiu visualizar os pacotes relacionados à transferência do arquivo.

![image](https://github.com/user-attachments/assets/a7d28e42-98a3-4a69-a947-c21c4a86481c)

Seguindo o mesmo procedimento anterior (Analyze → Follow → TCP Stream), conseguimos visualizar o conteúdo do arquivo transmitido em formato ASCII.
![image](https://github.com/user-attachments/assets/9912adf6-545f-4562-93db-c647ecb8d724)

Com isso já sabemos que temos que salvar esse aquivo com o nome de ```file.des3```, para que o código passado na conversa< ```openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123```, consiga descriptografar o arquivo.

Ao tentarmos com o arquivo salvo em ASCII ao usar o ssl vai aparecer o erro ```bad magic number```, mas é fácil contornar isso, basta voltarmos ao arquivo e salvar ele em ```raw```.
![image](https://github.com/user-attachments/assets/b666d6f4-76e1-49a9-9330-d6442a05226c)

## 🔑 Recuperando a Flag

Agora que identificamos o arquivo transmitido, precisamos salvá-lo com o nome correto:
```file.des3```
Em seguida, utilizamos o comando de descriptografia mencionado na conversa:
```openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123```
No entanto, ao tentar descriptografar o arquivo salvo diretamente em ASCII, o erro "bad magic number" aparece. Para corrigir isso, devemos salvar o arquivo no formato RAW em vez de ASCII.
![image](https://github.com/user-attachments/assets/0bb69bdb-31e3-4c36-93f8-12263223be04)

Após salvar corretamente, repetimos o comando de descriptografia:
```openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123```
Isso gera um arquivo chamado file.txt. Para visualizar o conteúdo e obter a flag, utilizamos:
```cat file.txxt```
![image](https://github.com/user-attachments/assets/e7c77cb5-c691-43d2-bb0d-73ae07232dca)


> Assim, obtemos a flag `picoCTF{nc_73115_411_dd54ab67} `
