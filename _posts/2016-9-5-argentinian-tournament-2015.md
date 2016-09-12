---
layout: post
title: Argentinian Programming Tournament 2015
categories: simulados
---

Essa foi uma prova organizada pelo nosso *coach* na semana de break do BCC.

### Recursos
* [Problemas](http://torneoprogramacion.com.ar/wp-content/uploads/2015/09/pruebaTAP2015.pdf)
* [Placar oficial](https://icpc.baylor.edu/regionals/finder/tap-2015/standings)
* [Placar do simulado](https://www.codepit.io/#/contest/57cc82a27f4d2d1900761fbe/view)

### Comentários

Nosso desempenho nessa prova deixou a desejar. Com a pontuação final de 6 problemas,
resolvemos o problema J logo após o fim da prova e sabíamos fazer o problema A.

Um fator complicador foi o fato de que um dos nosso integrantes pode fazer apenas metade da prova,
o que dificultou a dinâmica do time.

Apesar disso, nosso desempenho garantiria o 5o lugar na Argentina.

### Problema

O problema destaque da prova foi o [**Problema B (Good kg of Flauta bread)**](http://www.spoj.com/problems/TAP2015B/), que pode ser reduzido para:

**Dada um triângulo de altura L, divida ele em K secções paralelas a base de tal forma que a menor secção (em número de triângulos pequenos) seja máxima.** (Vide figura)

![SPOJ TAP2015B](http://www.spoj.com/content/fidels:TAP2015B3.png)

Por exemplo, na figura L = 5 e K = 3. A escolha ótima é a primeira pois a menor secção contém 7 triângulos.

Vamos supor que X seja uma resposta (não necessariamente maximal). Sabemos que se existe uma partição válida em K' <= K partes, podemos particionar em K partes mantendo a invariante de X como resposta, pois apenas aumentaremos o tamanho de pelo menos uma das secções.

**Assim, se X é resposta, X - 1 também é. Com essa garantia, podemos aplicar Busca Binária na resposta!**

Fixamos um X, testamos se é resposta. Se for, procuramos uma resposta melhor X' > X. Caso contrário tentamos X' < X.

```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://www.spoj.com/problems/TAP2015B/
*/
#include <bits/stdc++.h>
#define debug(args...) fprintf(stderr, args)
#define ff first
#define ss second

using namespace std;

typedef long long lint;
typedef pair<int, int> pii;

const int MAXL = 1123456;
lint sum[MAXL];

int main() {
	int l, k;
	while(scanf("%d%d", &l, &k) != EOF) {
		lint sum = 0;
		for(int i = 0; i < l; i++) sum += 1 + 2*i;
		lint lo = 0, hi = sum, ans = 0;
		while(lo <= hi) {
			lint mid = (lo + hi) / 2;
			int idx = 0, cnt = 0;
			while(idx < l && cnt < k) {
				lint s = 0;
				while(s < mid && idx < l) {
					s += 1 + 2*idx;
					idx++;
				}
				if(s >= mid) cnt++;
			}
			if(cnt == k) {
				ans = mid;
				lo = mid + 1;
			} else hi = mid - 1;
		}
		printf("%lld\n", ans);
	}
	return 0;
}
```
