---
layout: post
title: Sub-regional 2009
categories: simulados
---

Com a proximidade da sub-regional (que será dia 10/09), decidimos nos focar em fazer
as provas de mesmo "estilo".

Como um dos meus colegas em equipe participa da Maratona desde 2012, decidimos optar por um contest mais antigo. O escolhido foi a Sub-regional de 2009.

Convidamos também as equipes *"Brenzo"* e *"All by myyyyyseeelf"*, formada exclusivamente de bixos, para participar conosco.

Isso ajuda pois, quanto mais informações tivermos no placar, melhor iremos distribuir a atenção nos problemas.

### Recursos
* [Problemas](http://maratona.ime.usp.br/hist/2009/primeira-fase/primeirafase09/maratona.pdf)
* [Placar oficial](http://maratona.ime.usp.br/hist/2009/primeira-fase/primeirafase09/saopaulo/score.php.htm)
* [Placar do simulado](https://www.codepit.io/#/contest/57c95e7d7f4d2d1900761d23/view)

### Comentários

Haviam 4 problemas fáceis (B, C, D, H) e 1 médio (E) e 3 difíceis (A, F, G).

O resultado da prova foi bastante positivo.

Fizemos 6 dos 8 problemas propostos: B, C, D, H, E e A.

Tal resultado garantiria 1º lugar na sede e a vaga pra final brasileira. Também ficariamos a frente da equipe *"e muita grana!"* do IME-USP, que classificou para a final mundial naquele ano.

Perdemos bastante tempo no problema H pois implementamos uma solução envolvendo Union-Find enquanto uma simples solução brute-force era suficiente para o Time Limit e no problema E, pois debugamos durante 1h e era um simples erro de digitação.

### Problema

O problema destaque da prova foi o [**Problema A (Ataque Fulminante)**](https://www.urionlinejudge.com.br/judge/pt/problems/view/1102), que pode ser reduzido para:

**Dado um circulo e uma região definida por duas semi-retas, encontre a área da intersecção destes.**

![URI 1102](https://urionlinejudge.r.worldssl.net/gallery/images/problems/UOJ_1102_pt.png)

Nossa primeira ideia foi utilizar integração numérica, pois a precisão requerida era apenas de uma casa decimal.

Com medo do erro sair do controle, abordamos o problema de outra forma. Sabiamos resolver o seguinte problema:

**Dado um circulo e um poligono simples, encontre a área da intersecção destes.**

Como as coordenadas era relativamente pequenas, transformamos a região num quadrilátero.

Para isso, primeiramente fixamos a "ponta" da região (intersecção entre as duas semi-retas), digamos **A**.

Depois escolhemos dois pontos bem distantes de **A** em cada uma das semi-retas, digamos **B** e **C**.

Como último bastava escolher qualquer ponto distante o suficiente da "ponta" que fosse interno à região. Uma escolha simples de implementar seria o ponto **D = (B - A) + (C - A)**, formando assim um paralelogramo.

Com esses pontos distantes o suficiente, garantimos que não haveria região não coberta por esse paralelogramo que seria coberta pela região original.

Depois da redução, aplicamos o algoritmo clássico e resolvemos o problema! =D

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    https://www.urionlinejudge.com.br/judge/pt/problems/view/1102
*/
#include <bits/stdc++.h>
#define pb push_back
#define mp make_pair
#define ff first
#define ss second
#define debug(args...) //fprintf(stderr, args)

using namespace std;

typedef long long lint;
typedef pair<int, int> pii;

const double Pi = 2*acos(0);

const double eps = 1e-6;

inline int cmp(double x, double y = 0, double tol = eps) {
    return (x <= y + tol) ? (x + tol < y) ? -1 : 0 : 1;
}

struct point {
    double x, y;
    point(double x = 0, double y = 0): x(x), y(y) {}
    point operator +(point q) { return point(x + q.x, y + q.y); }
    point operator -(point q) { return point(x - q.x, y - q.y); }
    point operator *(double t) { return point(x * t, y * t); }
    point operator /(double t) { return point(x / t, y / t); }
    double operator *(point q) {return x * q.x + y * q.y;}//a*b = |a||b|cos(ang)
    double operator %(point q) {return x * q.y - y * q.x;}//a%b = |a||b|sin(ang)
    double polar() { return ((y > -eps) ? atan2(y,x) : 2*Pi + atan2(y,x)); }
    double mod() { return sqrt(x * x + y * y); }
    double mod2() { return (x * x + y * y); }
    point rotate(double t) {return point(x*cos(t)-y*sin(t), x*sin(t)+y*cos(t));}
};

inline int ccw(point p, point q, point r) {
    return cmp((p - r) % (q - r));
}

double seg_distance(point p, point q, point r) {
    point A = r - q, B = r - p, C = q - p;
    double a = A * A, b = B * B, c = C * C;
    if (cmp(b, a + c) >= 0) return sqrt(a);
    else if (cmp(a, b + c) >= 0) return sqrt(b);
    else return fabs(A % B) / sqrt(c);
}

inline double angle(point p, point q, point r) {
    point u = p - q, v = r - q;
    return atan2(u % v, u * v);
}

typedef vector<point> polygon;

struct circle {
    point c;
    double r;
    circle(point c = point(0, 0), double r = 0) : c(c), r(r) {}
};

double area(point a, point b, circle S) {
    double aa=(S.c-a).mod(), bb=(S.c-b).mod(), cc=seg_distance(a, b, S.c);
    if (cmp(aa,S.r) <= 0 && cmp(bb,S.r) <= 0) return 0.5*fabs((a-S.c)%(b-S.c));
    if (cmp(cc,S.r) >= 0) return 0.5*fabs(S.r * S.r * angle(a, S.c, b));
    if (cmp(aa, bb) > 0) {swap(a, b); swap(aa,bb); }
    double A=(a-b).mod2(), B=2*((a-b)*(b-S.c)), C=(b-S.c).mod2()-S.r*S.r;
    double t = ((cmp(B*B-4*A*C)==0)?0.0: sqrt(B*B-4*A*C));
    double x1 = 0.5*(-B-t)/A, x2 = 0.5*(-B+t)/A;
    point p1 = a*x1 + b*(1-x1), p2 = a*x2 + b*(1-x2);
    if (cmp(aa, S.r) < 0) return area(a, p1, S) + area(p1, b, S);
    return area(a, p2, S) + area(p2, p1, S) + area(p1, b, S);
}

double area(polygon &T, circle S) {
    double ans=0.0;
    int n = (int)T.size();
    for (int i = 0; i < n; i++)
    ans += (area(T[i], T[(i+1)%n], S) * ccw(T[i], T[(i+1)%n], S.c));
    return fabs(ans);
}

int main() {
    circle circ;
    while(scanf("%lf%lf%lf", &circ.c.x, &circ.c.y, &circ.r) && circ.r > 0) {
        point can;
        double ang, t;
        scanf("%lf%lf%lf%lf", &can.x, &can.y, &ang, &t);
        ang = (Pi*ang)/180.0;
        t = (Pi*t)/180.0;
        point dest = can + point(cos(ang) * 1e6, sin(ang) * 1e6) ;
        polygon p;
        p.pb(can);
        p.pb((dest - can).rotate(-t/2) + can);
        p.pb(dest);
        p.pb((dest - can).rotate(t/2) + can);
        printf("%.1lf\n", area(p, circ));
    }
    return 0;
}
```
