---
layout: post
title: Argentinian Programming Tournament 2011
categories: simulados
---

As vésperas da final brasileira, continuamos nossa sequência de TAPs.

### Recursos
* [Problemas](http://www.dc.uba.ar/events/icpc/2011/taip-standings.html)
* [Placar oficial](https://icpc.baylor.edu/regionals/finder/torneo-argentino-2011/standings)
* [Placar do simulado](https://www.codepit.io/#/contest/581baedb27d7d8001a42cbb7/view)

### Comentários


### Problema

O problema destaque da prova foi o [**Problema F (Factory of Bridges)**](http://www.spoj.com/problems/FBRIDGES/).

São dadas duas sequências de semi-circulos alternando concavidades como na imagem abaixo:

![SPOJ FBRIDGES](http://www.spoj.com/content/pabloh:taip2011F.png)

É pedido para que se encontre a distância mínima entre os 2 lados.

Nossa primeira ideia foi derivar a função distância entre duas curvas que modelasse os semi-circulos. Como as contas de alongaram muito partimos para uma abordagem mais computacional.

Primeiramente é necessário notar que a distância entre dois semi-circulos é uma [[função unimodal](https://en.wikipedia.org/wiki/Unimodality). 

Sabendo isso podemos aplicar [busca ternária](https://en.wikipedia.org/wiki/Ternary_search) entre dois circulos para encontrar a distâcia mínima, sempre lembrando de testar os pontos extremos que também são candidatos.

Agora basta selecionar cada par de circulos que se intersectam, que pode ser feito com um simples *line sweep*.

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://www.spoj.com/problems/FBRIDGES/
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

struct event {
    int r, xc, x, up, nor;
    bool operator < (const event& e) {
        if(x != e.x) return x < e.x;
        return nor < e.nor;
    }
};

double y(double x, double r) {
    return sqrt(r*r - x * x);
}

event no, so;
int A;

double diff(double x) {
    return no.up*y(x - no.xc, no.r) - so.up*y(x - so.xc, so.r) + A;
}

double ter(double a, double b) {
    double r = min(diff(a), diff(b));
    for(int i = 0; i < 30; i++) {
        double m1 = a + (b - a)/3.;
        double m2 = a + 2.*(b - a)/3.;
        if(diff(m1) < diff(m2)) b = m2;
        else a = m1;
    }
    return min(diff(a), r);
}

int main() {
    int c;
    while(scanf("%d", &A) && A != -1) {
        vector<event> ve;
        int x = 0;
        char pos;
        scanf("%d", &c);
        scanf(" %c", &pos);
        int up = (pos == 'N') ? 1 : -1;
        for(int i = 0; i < c; i++) {
            int r;
            scanf("%d", &r);
            ve.pb({r, r + x, x, up, 1});
            up *= -1;
            x += 2 * r;
        }
        x = 0;
        scanf("%d", &c);
        scanf(" %c", &pos);
        up = (pos == 'N') ? 1 : -1;
        for(int i = 0; i < c; i++) {
            int r;
            scanf("%d", &r);
            ve.pb({r, r + x, x, up, 0});
            up *= -1;
            x += 2 * r;
        }
        sort(ve.begin(), ve.end());
        double resp = 1e60;
        for(int i = 0; i < ve.size(); i++) {
            int j;
            for(j = i; ve[j].x == ve[i].x && j < ve.size(); j++);
            for(int k = i; k < j; k++) {
                if(ve[k].nor) no = ve[k];
                else so = ve[k];
            }
            double a = max(no.x, so.x);
            double b = min(no.x + 2 * no.r, so.x + 2 * so.r);
            resp = min(resp, ter(a, b));
            i = j - 1;
        }
        printf("%.2lf\n", resp);
    }
    return 0;
}

```
