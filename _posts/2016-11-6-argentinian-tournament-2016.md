---
layout: post
title: Argentinian Programming Tournament 2016
categories: simulados
---

A "saideira", último contest antes da final.

### Recursos
* [Problemas](http://torneoprogramacion.com.ar/wp-content/uploads/2016/09/tap2016.pdf)
* [Placar oficial](http://torneoprogramacion.com.ar/2016/09/21/resultados-tap-2016/)
* [Placar do simulado](https://www.codepit.io/#/contest/581f67137fc9c300186511d2/view)

### Comentários

Outra prova boa! Agora é só pegar o voo e dar o máximo na final brasileira.

```
"Será que é tudo isso em vão?

Será que vamos conseguir vencer?"

--

Será - Legião Urbana
```

### Problema

O problema destaque da prova foi o [**Problema B (Finding the way)**](http://www.spoj.com/problems/TAP2016B/), que pode ser reduzido para:

**Dado um poligono convexo de N vértices, um ponto final A e um ponto inicial B, encontre o menor caminho que passe por todos os pontos que comece em A e termine em B**

Primeiramente separaremos o poligono em 2 subpoligonos U e L, sendo U o conjunto de pontos acima do segmento AB e L abaixo.

O essencial é perceber que é sempre subótimo um caminho se interceptar (fácil mostrar). Assim basta manter um ponteiro caminhando de A para B por U e outro ponteiro caminhando por L. Todos os pontos atrás e inclusive L e R estão visitados. Depois disso basta testas todas as possibilidades estando em U indo para U + 1 ou L + 1 depois estando em L indo para L + 1 ou R + 1. 

Como os estados são inteiros e ciclicos, basta montar uma DP.

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://www.spoj.com/problems/TAP2016B/
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

const int MAXN = 4123;
const double INF = 1e20;

double dp[MAXN][MAXN][2];
vector<int> up, lo;
int U, L;
vector<pii> ptos;

double sq(double x) { return x * x; }

double dist(int a, int b) {
    return sqrt(sq(ptos[a].ff - ptos[b].ff) + sq(ptos[a].ss - ptos[b].ss));
}

double f(int u, int l, int flag) {
    if(u == U) return l < L - 1 ? INF : 0;
    if(l == L) return u < U - 1 ? INF : 0;
    double &ret = dp[u][l][flag];
    if(ret == -1) {
        if(flag) {
            ret = min(f(u + 1, l, flag) + dist(up[u], up[u + 1]),
                f(u, l + 1, !flag) + dist(up[u], lo[l + 1]));
        } else {
            ret = min(f(u, l + 1, flag) + dist(lo[l], lo[l + 1]),
                f(u + 1, l, !flag) + dist(up[u + 1], lo[l]));
        }
    }
    return ret;
}

int main() {
    int n;
    while(scanf("%d", &n) != EOF) {
        up.clear();
        lo.clear();
        ptos.clear();
        int e, s;
        scanf("%d%d", &e, &s);
        e--; s--;
        for(int i = 0; i < n; i++) {
            pii pto;
            scanf("%d%d", &pto.ff, &pto.ss);
            ptos.pb(pto);
        }
        for(int i = e; i != s; i = (i + 1) % n) up.pb(i);
        up.pb(s);
        for(int i = e; i != s; i = (i - 1 + n) % n) lo.pb(i);
        lo.pb(s);
        U = up.size() - 1;
        L = lo.size() - 1;
        for(int i = 0; i <= U; i++) {
            for(int j = 0; j <= L; j++) {
                dp[i][j][0] = dp[i][j][1] = -1;
            }
        }
        printf("%.6lf\n", min(f(0, 0, 0), f(0, 0, 1)));
    }
    return 0;
}


```
