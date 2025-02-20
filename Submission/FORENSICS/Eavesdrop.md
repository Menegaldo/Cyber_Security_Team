# Eavesdrop
###### Resolvido por @Cubano2
> Este é um CTF sobre [ Capture of Files, Eavesdrop Messages, Forensic Data Extraction]  

## Sobre o Desafio  


## 🔗 Links do Desafio
[Eavesdrop](https://play.picoctf.org/practice/challenge/264) <br>
[Arquivo](https://artifacts.picoctf.net/c/134/capture.flag.pcap) <br>

### Análise Inicial
O desafio começa com o download do arquivo ```.pcap```, que pode ser analisado com o Wireshark. A dica fornecida pelo próprio CTF é:
```"Tudo o que sabemos é que esta captura de pacotes inclui uma conversa de chat e uma transferência de arquivo."```

Ao abrirmos o arquivo no Wireshark, nos deparamos com pacotes de protocolos variados.
![image](https://github.com/user-attachments/assets/47371886-e51a-4e75-ba2d-fae2e82ac28f)

## Solução  
O wireshark é uma ferramenta de captura de pacotes na qual ele nos permite filtrar por pacotes específicos e ver o contéudo transmitido caso a conexão não for segura. No caso o protocolo HTTP. Perguntando para o GPT sobre o pacote e sobre a dica que o exercício nos deu, ele me recomendou algumas opções de filtros para tentar, a maioria não foi bem sucedida mas o filtro que pegou exatamente o que eu queria foi o : ```tcp && data```
![image](https://github.com/user-attachments/assets/1c7dc7c3-3448-457a-aec6-7f93e25a4947)

![image](https://github.com/user-attachments/assets/66027c1a-4542-4512-bf9c-bc4f073e998b)


Analisando esses pacotes TCP e verificando a informação (data) que o pacote possuía, descobrimos uma conversa entre duas pessoas.
No wireshark acessando o menu -> Analyze -> Follow-> TCP stream :
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

Interceptando essa conversa percebemos que devemos filtrar os pacotes TCP na porta 9002. Filtrando no wireshark achamos esses pacotes.

![image](https://github.com/user-attachments/assets/a7d28e42-98a3-4a69-a947-c21c4a86481c)

Indo no Analyze -> Follow -> TCP stream conseguimos ver em ASCII o arquivo que foi transmitido entre as duas pessoas.
![image](https://github.com/user-attachments/assets/9912adf6-545f-4562-93db-c647ecb8d724)

Com isso já sabemos que temos que salvar esse aquivo com o nome de ```file.des3```, para que o código passado na conversa< ```openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123```, consiga descriptografar o arquivo.

Ao tentarmos com o arquivo salvo em ASCII ao usar o ssl vai aparecer o erro ```bad magic number```, mas é fácil contornar isso, basta voltarmos ao arquivo e salvar ele em ```raw```.
![image](https://github.com/user-attachments/assets/b666d6f4-76e1-49a9-9330-d6442a05226c)

Com isso é só usarmos o ```openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123``` no terminal mesmo e ele irá gerar um arquivo com o nome de ```file.txt```
![image](https://github.com/user-attachments/assets/0bb69bdb-31e3-4c36-93f8-12263223be04)

Sendo assim é só usarmos um ```cat file.txt``` para lermos o que tem nesse arquivo.
![image](https://github.com/user-attachments/assets/e7c77cb5-c691-43d2-bb0d-73ae07232dca)


> Assim, obtemos a flag `picoCTF{nc_73115_411_dd54ab67} `
