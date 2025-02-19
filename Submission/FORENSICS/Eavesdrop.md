# Eavesdrop
###### Resolvido por @Cubano2
> Este é um CTF sobre [Image Steganography, Incorrect File Extensions, Metadata Leakage, Hidden ZIP Files in Images, Forensic Data Extraction]  

## Sobre o Desafio  


## 🔗 Links do Desafio
[Eavesdrop](https://play.picoctf.org/practice/challenge/264) <br>
[Arquivo](https://artifacts.picoctf.net/c/134/capture.flag.pcap) <br>

### Análise Inicial
Esse desafio se inicia ao baixarmos o arquivo e abrirmos ele no wireshark, onde nos deparamos com esses pacotes de protocolos variados. A dica que a página ddo CTF nos deu é: ```Tudo o que sabemos é que esta captura de pacotes inclui uma conversa de chat e uma transferência de arquivo.```
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


