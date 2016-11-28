---
layout: post
title: Argentinian Programming Tournament 2014
categories: simulados
---

Mais provas argentinas!

### Recursos
* [Problemas](http://torneoprogramacion.com.ar/wp-content/uploads/2015/07/pruebaTAP2014.pdf)
* [Placar oficial](http://torneoprogramacion.com.ar/wp-content/uploads/2015/07/resultadosTAP2014.pdf)
* [Placar do simulado](https://www.codepit.io/#/contest/57fe7bd2a76425002ed3af8e/view)

### Comentários

Com 8 dos 11 problemas resolvidos, ficariamos em 3o lugar da Argentina.

O problema C nos prejudicou, uma vez que uma outra implementação de uma mesma ideia trocou o TL pelo AC. Apesar disso precisamos estar preparados para "escovar bits" caso necessário numa prova valendo.

### Problema

O problema destaque da prova foi o [**Problema F (String fertilization)**](http://www.spoj.com/problems/TAP2014F/), que pode ser reduzido para:

***É dado um conjunto de N strings inicialmente vazias e um conjunto de T queries.
A cada query é deletado C caracteres e adicionado um caractere dado no final de cada string.
É pedido qual é a p-ésima string (lexicograficamente) depois de cada query.***

Vamos primeiramente pensar na solução trivial:

No pior dos casos não apagamos nenhum caractere. Adicionamos e reordenamos todo o conjunto. Como ordenar N strings tem complexidade O(N\*logN\*CMP), onde CMP é o custo da comparação das trings.

Trivialmente passamos por todo caractere linearmente, ou seja, é proporcional ao número de caracteres logo de queries.

Assim temos O(T\*N\*logN\*T), que não é suficiente mas está perto.

A sacada aqui é notar que as operações são feitas apenas no fim da string, deste modo é manter um [Rolling hash](https://en.wikipedia.org/wiki/Rolling_hash).

Para comparar duas strings basta encontrar o maior prefixo comum entre elas e depois verificar o próximo caractere.

Encontrar o prefixo comum máximo pode ser encontrada com uma busca binária no hash, análogamente ao que é feito no [Suffix array](https://en.wikipedia.org/wiki/Suffix_array).

Complexidade final: O(T\*logT\*N\*logN)

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://www.spoj.com/problems/TAP2014F/
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

const int MAXN = 112, MAXL = 11234, BASE = 257, MOD = 1e9 + 7;

lint h[MAXN][MAXL];
char s[MAXN][MAXL];
int idx = 0;

bool cmp(int a, int b) {
    int lo = 1, hi = idx, ans = 0;
    while(lo <= hi) {
        int mid = (lo + hi) / 2;
        if(h[a][mid] == h[b][mid]) {
            lo = mid + 1;
            ans = mid;
        } else {
            hi = mid - 1;
        }
    }
    if(ans == idx) return a < b;
    else return s[a][ans + 1] < s[b][ans + 1];
}

int main() {
    int n, t, v[MAXN];
    scanf("%d %d", &n, &t);
    for(int i = 0; i < n; i++) v[i] = i;
    for(int i = 0; i < t; i++) {
        int c, p;
        char str[MAXN];
        scanf("%d %s %d", &c, str, &p);
        idx -= c; idx++;
        for(int j = 0; j < n; j++) {
            h[j][idx] = (h[j][idx - 1] * BASE + lint(str[j])) % MOD;
            s[j][idx] = str[j];
        }
        sort(v, v + n, cmp);
        printf("%d\n", v[p - 1] + 1);
    }
    return 0;
}

```
