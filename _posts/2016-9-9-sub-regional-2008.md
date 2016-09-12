---
layout: post
title: Sub-regional 2008
categories: simulados
---

Nada melhor que um ótimo desempenho para aumentar a confiança para amanhã! =D

### Recursos
* [Problemas](http://maratona.ime.usp.br/hist/2008/primeira-fase/maratona_v5.pdf)
* [Placar oficial](http://maratona.ime.usp.br/hist/2008/primeira-fase/resultados/saopaulo/score-detalhado.php.html)
* [Placar do simulado](https://www.codepit.io/#/contest/57d297b87f4d2d19007621a4/view)

### Comentários

Com 9 dos 10 problemas resolvidos antes de 4 horas de prova, foi um ótimo desempenho do time!

As ideias fluíram rápido e as implementações foram bem estruturadas.

### Problema

O problema destaque da prova foi o [**Problema I (Maior Subseqüência Crescente)**](https://www.urionlinejudge.com.br/judge/pt/problems/view/1092), que pode ser reduzido para:

**Dada uma matriz, encontre uma submatriz cuja linearização seja uma sequência crescente.**

![URI 1092](https://urionlinejudge.r.worldssl.net/gallery/images/problems/UOJ_1092.png)

Na imagem, a linearização da submatriz selecionada é *[21, 22, 31, 33, 43, 45, 51, 66, 83]*, que a maior sequência crescente.

Primeiramente vamos pré-calcular **pre[i][j]**: a maior sequência crescente a partir do elemento **(i, j)** até o fim da linha.

É fácil ver que **pre[i][j] = 1 + pre[i][j + 1]** se o elemento **mat[i][j] < mat[i][j + 1]**, caso contrário **pre[i][j] = 1**.

Depois pré-calularemos **dp[i][j]**: a maior sequência crescente a partir do elemento **(i, j)** que termine num elemento **(i, j')**, com **j' >= j**, tal que **mat[i][j'] < mat[i + 1][j]**. Ou seja, **dp[i][j]** tem o maior lado de quadrado possível que pode ser expandido para a linha debaixo.

O seu cálculo é feito com o auxílio da tablea **pre[i][j]** e de uma busca binária.

Agora, para cada coluna, reduzimos nosso problema ao seguinte: **Dada um conjunto de barras (uma por linha), encontre a região de maior área**, que é o clássico problema do histograma.

Após tudo isso, ainda havia a dificuldade de que **dp[i][j]** funcionava apenas quando a barra não era a última. Para contornar isso, era necessário grande conhecimento do algoritmo clássico e de manipulação de stacks, adicionando uma barra de largura nula para manter a invariante correta. 

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    https://www.urionlinejudge.com.br/judge/pt/problems/view/1092
*/
#include <bits/stdc++.h>
#define debug(args...) fprintf(stderr, args)
#define ff first
#define ss second
#define mp make_pair
#define pb push_back

using namespace std;

typedef pair<int, int> pii;
typedef long long lint;

const int MAX = 612;

int mat[MAX][MAX], pre[MAX][MAX], dp[MAX][MAX];

stack<pii> s;
lint area;

void add(int h, int l) {
	int cnt = 0;
	while(!s.empty() && h < s.top().first) {
		cnt += s.top().second;
		area = max(area, lint(cnt)*s.top().first);
		s.pop();
	}
	s.push({h, cnt + l});
}

int main() {
	int n, m;
	while(scanf("%d%d", &n, &m) && n) {
		area = 0;
		for(int i = 0; i < n; i++) {
			for(int j = 0; j < m; j++) {
				scanf("%d", &mat[i][j]);
				pre[i][j] = 0;
			}
			mat[i][m] = INT_MIN;
			for(int j = m - 1; j >= 0; j--) {
				pre[i][j] = ((mat[i][j] < mat[i][j + 1]) ? pre[i][j + 1] : 0) + 1;
			}
		}
		for(int i = 0; i < m; i++) mat[n][i] = INT_MAX;
		for(int i = 0; i < n; i++) {
			for(int j = 0; j < m; j++) {
				dp[i][j] = lower_bound(mat[i] + j, mat[i] + j + pre[i][j], mat[i + 1][j]) - (mat[i] + j);
			}
		}
		for(int j = 0; j < m; j++) {
			s = stack<pii>();
			for(int i = 0; i < n; i++) {
				add(pre[i][j], 1);
				add(dp[i][j], 0);
			}
			add(0, 0);
		}
		printf("%lld\n", area);
	}
	return 0;
}
```
