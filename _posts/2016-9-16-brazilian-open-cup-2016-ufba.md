---
layout: post
title: Brazilian Open Cup 2016, UFBA
categories: simulados
---

Com a classificação para Final Brasileira, mudamos a estratégia e agora estamos buscando provas com dificuldade mais elevada. A escolhida dessa vez foi um contest setado pela UFBA.

### Recursos
* [Problemas](http://codeforces.com/group/kZPk3ZTzR5/contest/101072/problems)
* [Placar](http://codeforces.com/group/kZPk3ZTzR5/contest/101072/standings/groupmates/true) (escolher opção *show unofficial*)

### Comentários

Estavamos receosos de fazermos uma prova longa (12 problemas) e de dificuldade mais elevada por estarmos em 2 (eu e o André) mas decidimos encarar mesmo assim.

Embora no placar estejam faltando muitas equipes boas que estarão na final, mas os times que estavam (UNIFEI, ITA e UFPE) colocaram certa pressão e possibilitaram que medíssemos nosso desempenho.

Ficamos em 3o lugar, com 9 de 12 problemas resolvidos.

Eu e o André desenvolvemos um entrosamento muito forte de tal forma que eu consigo implementar suas ideias de maneira estruturada e quase sem bugs, fazendo com que sobre tempo para que ele encontre a solução dos problemas mais difíceis.

### Problema

Gostaria de comentar sobre o [**Problema E (Conectando Cidades II
)**](http://codeforces.com/group/kZPk3ZTzR5/contest/101072/problem/E), que pode ser reduzido para:

**Dado N pontos no plano, qual o mínimo de arestas direcionadas necessárias para que os pontos formem uma componente fortemente conexa, não sendo permitido a intersecção de arestas em nenhum ponto exceto os extremos. Imprima tais arestas.**

É necessario observar primeiramente que o limite inferior para o número de arestas é **N**. Nosso algoritmo sempre constroi uma solução com **N** arestas, logo não precisaremos nos preocupar com a minimização.

A ideia é natural para quem teve alguma experiência com Convex Hull montado via [**Graham scan**](https://en.wikipedia.org/wiki/Graham_scan).

Ordenando os pontos baseados num ângulo em relação ao pivô (ponto extremo), é fácil notar que basta seguir a ordem que este poligono não terá self-crossings.

| Pontos        | Poligono           |
| ------------- |:-------------:|
| ![Pontos]({{site.baseurl}}/images/2016-9-16-brazilian-open-cup-2016-ufba/pontos.png)      | ![Poligono]({{site.baseurl}}/images/2016-9-16-brazilian-open-cup-2016-ufba/poligono.png)
 |

Como nem tudo na vida são flores, existem corner cases. Em caso de empate de ângulo (colineares em relação ao pivô), queremos escolher os primeiro os mais próximos, pois para visitar um ponto distante precisaremos passar por ele antes.

A parte tênue é que isso é verdade para quaisquer pontos colineares exceto os pertencentes ao último segmento pois, para "fechar" o ciclo, precisamos terminar o ponto mais próximo (pivô) e começar no mais distante.

Depois era necessário atenção apenas aos *corner cases*, como a existência apenas de um segmento (poligono degenerado) ou a existência de menos de 3 pontos.

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://codeforces.com/group/kZPk3ZTzR5/contest/101072/problem/E
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

vector<pii> v;

lint cross(pii c, pii a, pii b) {
    a.ff -= c.ff;
    a.ss -= c.ss;
    b.ff -= c.ff;
    b.ss -= c.ss;
    return lint(a.ff) * b.ss - lint(a.ss) * b.ff;
}

lint dist(pii a, pii b) {
    return lint(a.ff - b.ff) * lint(a.ff - b.ff) + lint(a.ss - b.ss) * lint(a.ss - b.ss);
}

bool cmp(pii &a, pii &b) {
    if(cross(v[0], a, b) == 0) return dist(v[0], a) < dist(v[0], b);
    return cross(v[0], a, b) > 0;
}

int main() {
    map<pii, int> m;
    int n;
    scanf("%d", &n);
    if(n == 2) {
        printf("-1\n");
        return 0;
    }
    for(int i = 0; i < n; i++) {
        int x, y;
        scanf("%d%d", &x, &y);
        v.pb({x, y});
        m[{x, y}] = i + 1;
    }
    sort(v.begin(), v.end());
    sort(v.begin() + 1, v.end(), cmp);
    int ii;
    for(ii = n - 2; ii >= 0; ii--) {
        if(cross(v[0], v[ii], v[ii + 1]) != 0) break;
    }
    ii++;
    if(ii == 0) {
        printf("-1\n");
        return 0;
    }
    for(int i = ii; i < n; i++) {
        if(n - i + ii - 1 < i) break;
        swap(v[i], v[n - i + ii - 1]);
    }
    printf("%d\n", n);
    for(int i = 0; i < n; i++) printf("%d %d\n", m[v[i]], m[v[(i + 1) % n]]);
    return 0;
}
```
