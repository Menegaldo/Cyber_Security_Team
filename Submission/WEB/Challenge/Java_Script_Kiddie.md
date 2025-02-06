# Java Script Kiddie  
###### Resolvido por @Menegaldo e @BrunoCuba  
> Este é um CTF sobre [Tipo de Vulnerabilidade 1, Tipo de Vulnerabilidade 2 e Tipo de Vulnerabilidade 3]  

## Sobre o Desafio

The image link appears broken...

## 🔗 Links do Desafio  
- [Acesse o desafio - Link 1](https://jupiter.challenges.picoctf.org/problem/17205)  
- [Acesse o desafio - Link 2](http://jupiter.challenges.picoctf.org:17205)  

## Solução  

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. [Nemo enim](instagram.com/duckwareteam/) ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit.  

![Este é um texto alternativo.](/images/example.jpg "Esta é uma imagem de exemplo")  

sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet:  

```
// Inicializa o array que armazenará os números primos
var primeArray = [];
/* Escreve uma função que verifica se um número é primo e
adiciona esses valores ao array */
function PrimeCheck(candidate){
  isPrime = true;
  for(var i = 2; i < candidate && isPrime; i++){
    if(candidate%i === 0){
      isPrime = false;
    } else {
      isPrime = true;
    }
  }
  if(isPrime){
    primeArray.push(candidate);
  }
  return primeArray;
}
/* Escreve o código que executa a função acima até que
o tamanho do array seja igual ao número desejado de primos */

var numPrimes = prompt("Quantos números primos?");

// Exibe o array final de números primos

// Loop for começando em 2, pois é o menor número primo, e continua até que o array tenha o tamanho desejado
for (var i = 2; primeArray.length < numPrimes; i++) {   
    PrimeCheck(i); //
}
console.log(primeArray);
```

> Assim, obtemos a flag `DCK{FL_4G}`  
