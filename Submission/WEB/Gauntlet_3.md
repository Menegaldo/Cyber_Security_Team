# Gauntlet 3
###### Resolvido por @Menegaldo e @BrunoCuba
> Este é um CTF sobre [SQL Injection, Weak Filtering, Session Manipulation]

## Sobre o Desafio  
Last time, I promise!  

**Objetivo:** Log in as admin. 

## 🔗 Links do Desafio

- [Gauntlet_3](https://play.picoctf.org/practice/challenge/128)
- [Acesse o site](http://mercury.picoctf.net:8650/)  
- [Página de filtragem](http://mercury.picoctf.net:8650/filter.php)    

## Solução  

Acessei o site com essa tela de login:<br>

![image](https://github.com/user-attachments/assets/894f1710-9ef2-4e1e-8d6c-f9b3cf4e3321)

E vi que na url ```http://mercury.picoctf.net:32946/filter.php``` continha os mesmos filtros do Web Gauntlet 2.<br>

![image](https://github.com/user-attachments/assets/e665f986-4d37-4d1b-94ae-722b0079b467)


Com isso minha primeira tentativa foi usar o Login que usei no Web Gauntlet 2, LOGIN:```ad'||'min``` SENHA:```1' IS NOT '2```.
E acabou funcionando!<br>

![image](https://github.com/user-attachments/assets/86f0695b-065b-435b-84b3-4c05235fc10d)

![image](https://github.com/user-attachments/assets/8eae02cb-ddd8-4f51-9d7d-aec65f7337e7)

Com isso o site mandou eu visitar o link ```http://mercury.picoctf.net:32946/filter.php``` novamente e me apresentou o código a seguir:<br>

```php
<?php
session_start();

if (!isset($_SESSION["winner3"])) {
    $_SESSION["winner3"] = 0;
}
$win = $_SESSION["winner3"];
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
    $_SESSION["winner3"] = 0;        // <- Don't refresh!
} else {
    $_SESSION["winner3"] = 0;
}

// picoCTF{k3ep_1t_sh0rt_ef4a5b40aa736f5016b4554fecb568d0}
?>
```

> Assim, obtemos a flag `picoCTF{k3ep_1t_sh0rt_ef4a5b40aa736f5016b4554fecb568d0}`  
