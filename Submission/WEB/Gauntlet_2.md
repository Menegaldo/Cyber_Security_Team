# Gauntlet 2
###### Resolvido por @Menegaldo e @BrunoCuba 
> Este é um CTF sobre [Tipo de Vulnerabilidade 1, Tipo de Vulnerabilidade 2 e Tipo de Vulnerabilidade 3]  

## Sobre o Desafio  
This website looks familiar...  

**Objetivo:** Log in as admin Site:  

## 🔗 Links do Desafio  
- [Acesse o site](http://mercury.picoctf.net:21336/)  
- [Página de filtragem](http://mercury.picoctf.net:21336/filter.php)  

Boa sorte! 🔥  

## Solução  









<?php
session_start();

if (!isset($_SESSION["winner2"])) {
    $_SESSION["winner2"] = 0;
}
$win = $_SESSION["winner2"];
$view = ($_SERVER["PHP_SELF"] == "/filter.php");

if ($win === 0) {
    $filter = array("or", "and", "true", "false", "union", "like", "=", ">", "<", ";", "--", "/*", "*/", "admin");
    if ($view) {
        echo "Filters: ".implode(" ", $filter)."<br/>";
    }
} else if ($win === 1) {
    if ($view) {
        highlight_file("filter.php");
    }
    $_SESSION["winner2"] = 0;        // <- Don't refresh!
} else {
    $_SESSION["winner2"] = 0;
}

// picoCTF{0n3_m0r3_t1m3_e2db86ae880862ad471aa4c93343b2bf}
?>

> Assim, obtemos a flag `DCK{FL_4G}`  
