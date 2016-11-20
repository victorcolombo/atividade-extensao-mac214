---
layout: post
title: Asia Amritapuri 2012
categories: simulados
---

Uma prova para ser esquecida :P

### Recursos
* [Problemas](https://www.codechef.com/ACMAMR15)
* [Placar oficial](https://icpc.baylor.edu/regionals/finder/amritapuri-2015/standings)
* [Placar do simulado](https://www.codepit.io/#/contest/58133d1782fd150019942666/view)

### Comentários

Como nosso pior desempenho desde o inicio do nosso treinamento, eu e o André resolvemos apenas 4 dos 10 problemas propostos.

Tudo que poderia dar errado deu. André não conseguiu ter ideia para os problemas mais difíceis da prova e eu errei na implementação do problema A, tomei WA durante o contest e não consegui debugar.

Obs: Novamente o mesmo problema. Embora o problema C apareça como WA no Maratonando, ele foi aceito pelo SPOJ. Ocorreu um erro no juiz Live Archive durante o contest.

### Problema

Essa prova teve diversos problemas interessantes, principalmente os problemas C e F (ambos de DP).

Tanto o C quanto o F são bem complexos de serem explicados, então discutirei o [Problema H (Longest Palindrome)](https://icpcarchive.ecs.baylor.edu/index.php?option=onlinejudge&Itemid=99999999&category=720&page=show_problem&problem=5583).

Enunciado: N pares de letras, sendo elas "aa", "ab", "ba" e "bb" monte o menor palindromo lexicograficamente de tamanho máximo.

Neste problema aplicamos uma abordagem gulosa. Colocaremos pares nos extremos da string, de "fora pra dentro". Note que não é possível colocar pares diferentes nos extremos. Assim, colocamos nos extremos todos os pares 'aa' possíveis, depois 'ab' na ponta esquerda e 'ba' na ponta direita e por fim terminamos com 'bb'.

Era necessário tomar cuidado pois era possível inserir um par no "meio" da string, sem que ela tenha um correspondente. Por exempo 'aa' + 'bb' + 'aa', 'aa' tem correspondente mas 'bb' não.

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    https://icpcarchive.ecs.baylor.edu/index.php?option=onlinejudge&Itemid=99999999&category=720&page=show_problem&problem=5583
*/
#include <bits/stdc++.h>
#define pb push_back
#define mp make_pair
#define ff first
#define ss second
#define debug(args...) fprintf(stderr, args)

using namespace std;

typedef long long lint;
typedef pair<int, int> pii;

int main() {
    int t;
    scanf("%d", &t);
    while(t--) {
        int n;
        int aa = 0, ab = 0, bb = 0, ba = 0;
        scanf("%d", &n);
        for(int i = 0; i < n; i++) {
            string s;
            cin >> s;
            if(s == "aa") aa++;
            else if(s == "bb") bb++;
            else if(s == "ab") ab++;
            else ba++;
        }
        for(int i = 0; i < aa/2; i++) printf("aa");
        for(int i = 0; i < min(ab, ba); i++) printf("ab");
        for(int i = 0; i < bb/2; i++) printf("bb");
        if(aa & 1) printf("aa");
        else if(bb & 1) printf("bb");
        for(int i = 0; i < bb/2; i++) printf("bb");
        for(int i = 0; i < min(ab, ba); i++) printf("ba");
        for(int i = 0; i < aa/2; i++) printf("aa");
        printf("\n");
    }
    return 0;
}

}
```
