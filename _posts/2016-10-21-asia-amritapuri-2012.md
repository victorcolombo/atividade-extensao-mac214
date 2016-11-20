---
layout: post
title: Asia Amritapuri 2012
categories: simulados
---

Essa foi uma prova muito interessante, recomendada pelo nosso *coach* Renzo Gonzales.

### Recursos
* [Problemas](http://icpc.amrita.ac.in/problemSet.html)
* [Placar oficial](https://icpc.baylor.edu/regionals/finder/amritapuri-2012/standings)
* [Placar do simulado](https://www.codepit.io/#/contest/580a0a72fcdad400183b1dc0/view)

### Comentários

Essa prova foi feita em 2 pessoas (André e eu). Conseguimos resolver 7 problemas, o que garantiria pelo menos 5o lugar na região. O problema diferencial foi o problema H, um problema muito diferente do que estamos acostumados, envolvendo esperança, árvores de expressão e integração.

Obs: Embora o problema H apareça como WA no Maratonando, ele foi aceito pelo SPOJ. Ocorreu um erro no juiz Live Archive durante o contest.

### Problema

O problema destaque da prova foi o [**Problema E (Dyslexic Gollum)**](http://www.spoj.com/problems/AMR12E/), que pode ser reduzido para:

**Quantas strings de tamanho N existem tal que não exista uma substring palíndroma de tamanho pelo menos K.**

A principal observação do problema era que K << N, assim poderíamos manter alguma informação interessante na ordem de 2^K.

É importante notar que se uma substring S[a..b] é palíndroma, S[(a + 1)..(b - 1)] também é. Assim, bastávamos detectar palíndromos de tamanho K e K + 1 (para tratar palíndromos ímpares).

Montamos assim uma recorrência na forma:

f(n, S) = número de palavras palíndomas de tamanho n formamos dado que já existe um prefixo S.
f(0, S) = 1 (existe uma palavra de tamanho 0)
f(n, S') = 0 (se S' é palindroma)

Assim:

f(n, S) = f(n - 1, trim(S + 1)) + f(n - 1, trim(S + 0))

Note que o operador + aplicado no S não é o operador adição e sim concatenação. A função trim() é uma função para cortar a palavra, mantendo ela sempre de tamanho K + 1.

Para implementar S, trim() e +, usamos um número binário e manipulação de bits (bit shift, módulo, etc).

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://www.spoj.com/problems/AMR12E/
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

const int MAXN = 412, MAXK = 11, MOD = 1000000007;

int n, k;
int ispal[1 << (MAXK + 1)][MAXK + 1];

bool pal(int pat, int sz) {
    if(ispal[pat][sz] == -1) {
        int x[15];
        ispal[pat][sz] = 1;
        for(int i = 0; i < sz; i++) x[i] = (pat >> i) % 2;
        for(int i = 0; i < sz; i++) if(x[i] != x[sz - i - 1]) ispal[pat][sz] = 0;
    }
    return ispal[pat][sz];
}


lint dp[MAXN][1 << MAXK];

lint f(int q, int s) {
    if(q == k && pal(s, k)) return 0;
    if(q == n) return 1;
    if(dp[q][s] == -1) {
        dp[q][s] = 0;
        if(q < k) {
            dp[q][s] += f(q + 1, (s << 1) % (1 << k));
            dp[q][s] += f(q + 1, ((s << 1) + 1) % (1 << k));
        } else {
            if(!pal(s << 1, k) && !pal(s << 1, k + 1)) dp[q][s] += f(q + 1, (s << 1) % (1 << k));
            if(!pal((s << 1) + 1, k) && !pal((s << 1) + 1, k + 1)) dp[q][s] += f(q + 1, ((s << 1) + 1) % (1 << k));
        }
        dp[q][s] %= MOD;
    }
    return dp[q][s];
}


int main() {
    for(int i = 0; i < (1 << (MAXK + 1)); i++) {
        for(int j = 0; j < MAXK + 1; j++) {
            ispal[i][j] = -1;
        }
    }
    int t;
    scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &k);
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < (1 << k); j++) {
                dp[i][j] = -1;
            }
        }
        printf("%lld\n", f(0, 0));
    }
    return 0;
}
```
