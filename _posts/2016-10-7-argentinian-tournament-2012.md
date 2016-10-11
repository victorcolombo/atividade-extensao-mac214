---
layout: post
title: Argentinian Programming Tournament 2012
categories: simulados
---

Seguimos com a estratégia de fazer as TAPs antigas, já que estas possuem diversos problemas *standards* e alguns problemas difíceis, como a Final Brasileira.

### Recursos
* [Problemas](http://torneoprogramacion.com.ar/wp-content/uploads/2015/07/pruebaTAP2012.pdf)
* [Placar oficial](https://icpc.baylor.edu/regionals/finder/torneo-argentino-2012/standings)
* [Placar do simulado](https://www.codepit.io/#/contest/57f7aca6a76425002ed3ae22/view)

### Comentários

Embora nessa prova nossa precisão tenha diminuido (tomamos 7 WAs ao contrário da nossa média de ~3), ainda conseguimos um ótimo desempenho. A responsabilidade da maior parte desses erros foi minha que implementei uma ideia errada num problema e errei limite em outros dois.

Fizemos 7 dos 9 problemas propostos, o que nos garantiria novamente 1o lugar na Argentina!

Três problemas fizeram a diferença nessa prova, mostrando como as qualidades individuais são essenciais: o problema C (implementação + dp nos dígitos, minha especialidade), o problema E (dp difícil, especialidade do André) e o problema D, que merece estar na discussão de hoje.

### Problema

O problema destaque da prova foi o [**Problema D (Designing T-Shirts)**](http://www.spoj.com/problems/TAP2012D/), que pode ser reduzido para:

**Dado um grafo bipartido completo G(A, B), encontre o emparelhamento de valor máximo, onde o valor de cada aresta é o maior prefixo comum (LCP) entre as strings associadas aos nós que ela liga.**

Por exemplo, tomando um conjunto de strings A = {"Victor", "Vitória"} e B = {"Vitorioso", "Veneza"}, o emparelhamento de valor máximo é "Vitória" - "Vitorioso" e "Victor" - "Veneza" pois LCP("Victor", "Veneza") + LCP("Vitória", "Vitorioso") = 1 + 3 = 4 sendo esse o valor máximo neste caso.

Como as strings eram muitas (N <= 10 ^ 4) era necessário um método eficiente para calcular o LCP entre todo o par de strings. Para isso, concatenamos todas as strings (é dado que a soma de todas as strings não ultrapassa 10 ^ 5 caracteres) e montamos o [Suffix Array](http://web.stanford.edu/class/cs97si/suffix-array.pdf) dessa strings.

Depois disso montamos uma [Sparse Table](https://www.topcoder.com/community/data-science/data-science-tutorials/range-minimum-query-and-lowest-common-ancestor/) de mínimos para realizar a consulta do LCP em O(1).

Além disso era necessário algum pré-processamento para encontrar o posicionamento das strings originais do Suffix Array.

Montamos o grafo e rodamos o clássico algoritmo MinCost-MaxFlow e... TLE :(

O algoritmo por si só já é pesado, ainda mais realizando num grafo completo com os valores no limite. Era necessária outra abordagem!

Após fazermos alguns casos manualmente percebemos que uma estratégia gulosa funcionava (publicarei depois a prova do porquê). Bastava escolher o par com maior LCP tal que nenhum deles já tivesse sido escolhido e repetir.

Ou seja, ordenamos cada par de LCP e comemoramos o... TL :((

A única parte "pesada" do nosso programa era a ordenação, que deixava o código O(N^2lgN). Precisávamos retirar o fator log.

Ao perceber que os valores dos LCPs eram inteiros e limitados ao tamanho da maior string, realizamos [Counting Sort](https://en.wikipedia.org/wiki/Counting_sort) para um AC sofrido e comemorado :D

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://www.spoj.com/problems/TAP2012D/
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

const int MAXN = 11234;

// SUFFIX ARRAY

const int LEN = 110000, LOG = 21;

int len, suff[LEN], rmq[LEN][LOG], lg[LEN], lcp[LEN];
char str[LEN];

bool cmp(int a, int b) {
	return str[a] < str[b];
}

void buildSA() {
	int order[LEN], classes[LEN];
	for(int i = 0; i < len; i++)
		order[i] = len - 1 - i;
	std::stable_sort(order, order + len, cmp);
	for(int i = 0; i < len; i++) {
		suff[i] = order[i];
		classes[i] = str[i];
	}
	for(int j = 1; j < len; j *= 2) {
		int c[LEN], cnt[LEN], s[LEN];
		for(int i = 0; i < len; i++)
			c[i] = classes[i];
		for(int i = 0; i < len; i++) {
			classes[suff[i]] = 	i > 0 &&
								c[suff[i - 1]] == c[suff[i]] &&
								suff[i - 1] + j < len &&
								c[suff[i - 1] + j / 2] == c[suff[i] + j / 2] ? classes[suff[i - 1]] : i;
			cnt[i] = i;
			s[i] = suff[i];
		}
		for(int i = 0; i < len; i++) {
			int s1 = s[i] - j;
			if(s1 >= 0)
				suff[cnt[classes[s1]]++] = s1;
		}
	}
}

void buildLCP() {
	int rank[LEN];
	for(int i = 0; i < len; i++)
		rank[suff[i]] = i;
	for(int i = 0, h = 0; i < len; i++) {
		if(rank[i] < len - 1)
			for(int j = suff[rank[i] + 1]; std::max(i, j) + h < len && str[i + h] == str[j + h]; ++h);
		lcp[rank[i]] = h;
		if(h > 0) h--;
	}
}

void calcLog() {
	for(int i = 1; i < LEN; i++) {
		lg[i] = 0;
		for(int aux = i; aux > 1; aux /= 2, lg[i]++);
	}
}

void buildRMQ() {
	calcLog();
	for(int i = 0; i < len - 1; i++) {
		rmq[i][0] = lcp[i];
	}
	for(int i = 1; (1 << i) < len; i++) {
		for(int j = 0; (1 << i) + j < len; j++) {
			rmq[j][i] = std::min(rmq[j][i - 1], rmq[j + (1 << (i - 1))][i - 1]);
		}
	}
}

int queryLCP(int i, int j) {
    if(j < i) swap(i, j);
	j--;
	int k = lg[j - i + 1];
	return std::min(rmq[i][k], rmq[j - (1 << k) + 1][k]);
}

void build() {
	buildSA();
	buildLCP();
	buildRMQ();
}

int va[MAXN], vb[MAXN], la[MAXN], lb[MAXN], oka[MAXN], okb[MAXN];
char auxs[LEN];
int rev[LEN];
vector<pii> v[LEN];

int main() {
    int n;
    while(scanf("%d", &n) && n != -1) {
        int k = 0, maxi = 0;
        for(int i = 0; i < n; i++) oka[i] = okb[i] = 0;
        for(int i = 0; i < n; i++) {
            scanf(" %s", auxs);
            int l = la[i] = strlen(auxs);
            maxi = max(maxi, l);
            va[i] = k;
            for(int j = 0; j < l; j++) {
                str[k] = auxs[j];
                k++;
            }
        }
        for(int i = 0; i < n; i++) {
            scanf(" %s", auxs);
            int l = lb[i] = strlen(auxs);
            maxi = max(maxi, l);
            vb[i] = k;
            for(int j = 0; j < l; j++) {
                str[k] = auxs[j];
                k++;
            }
        }
        str[k] = 0;
        len = k;
        debug("string = %s\n", str);
        build();
        debug("Suffix array\n");
        for(int i = 0; i < len; i++) {
            debug("%d = %s\n", i, str + suff[i]);
            rev[suff[i]] = i;
        }
        for(int i = 0; i <= maxi; i++) v[i].clear();
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < n; j++) {
                int cc = min(min(la[i], lb[j]), queryLCP(rev[va[i]], rev[vb[j]]));
                v[cc].pb(mp(i, j));
            }
        }
        int sum = 0;
        for(int i = maxi; i >= 0; i--) {
            for(int j = 0; j < v[i].size(); j++) {
                if(!oka[v[i][j].ff] && !okb[v[i][j].ss]) {
                    oka[v[i][j].ff] = 1;
                    okb[v[i][j].ss] = 1;
                    sum += i;
                }
            }
        }
        printf("%d\n", sum);
    }
    return 0;
}
```
