---
layout: post
title: Argentinian Programming Tournament 2013
categories: simulados
---

"CALOVENTOR TIENE MIEDO"

### Recursos
* [Problemas](http://torneoprogramacion.com.ar/wp-content/uploads/2015/07/pruebaTAP2013.pdf)
* [Placar oficial](http://torneoprogramacion.com.ar/wp-content/uploads/2015/07/resultadosTAP2013.pdf)
* [Placar do simulado](https://www.codepit.io/#/contest/57f028c8dae9870018e8b472/view)

### Comentários

Mais um contest em que nosso treino mostrou efeito. Eu e o Ramon implementamos os problemas com velocidade de precisão enquanto o André pensava nos problemas mais difíceis, conseguindo sucesso.

Ao final da 4a hora de prova já tinhamos resolvido 9 dos 10 problemas. Com esse resultado seriamos campeões argentinos.

Tentamos fechar a prova porém encontramos e implementamos uma solução O(N^3logR) para o problema F, mas tomamos TLE. Desconfiamos que há uma solução O(N^3) para ele.

### Problema

O problema destaque da prova foi o [**Problema H (Horace and his primes)**](http://www.spoj.com/problems/TAP2013H/). Esse problema não dá para ser resumido em poucas palavras =/

Seja T(n) a soma dos fatores primos de n.

Exemplo: T(60) = T(2 * 2 * 3 * 5) = 2 + 3 + 5 = 10.

Seja K(n) = k, onde k é o menor inteiro tal que T(T(...T(n))) = n (T iterado k vezes).

Exemplo: K(60) = 3 pois T(T(T(60))) = T(T(10)) = T(7) = 7, ou seja, é necessário iterar T três vezes para chegar num primo.

Dado A, B, C <= 10^6, o problema pede para contar quantos A <= x <= B tal que K(x) = C.

Vamos primeiro pensar como pré-calcularemos todos os K's possíveis.

Para encontrar todos os fatores primos de um intervalo de números rapidamente utilizamos a bem conhecida técnica do Crivo de Erastótenes (https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes). Como K é uma relação de recorrência bem ordenada (K(x) = 1 + K(T(x)) e T(x) <= x), utilizamos programação dinâmica para resolver o sub-problema.

Ainda precisamos iterar sob o intervalo para responder a pergunta original. Isso é insuficiente em casos em que serão feitas múltiplas consultas (como esse).

Gostei especialmente desse problema pois ele representa o que acho mais interessante sobre a Maratona de Programação: intuição.

Intuição: O maior K(x) é pequeno. Ora, sabemos que os fatores primos de x são muito poucos (limitado superiormente a log2(x)), além de que estamos trocando produtos por somas. Após rodar um programa brute-force com o que já tinhamos percebemos que o máximo K para os valores da entrada era 12.

Dessa forma podiamos manter uma soma acumulada das frequências de cada K, respondendo cada consulta em O(1).


```c++
/*
    Victor Colombo - victor (dot) colombo (at) usp (dot) br
    http://www.spoj.com/problems/TAP2013H/
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

const int MAX = 1e6 + 1, MAX2 = 15;

int sieve[MAX], dp[MAX], sum[MAX][MAX2];
int maxi = -1;

int main() {
    for(int i = 2; i <= MAX; i++) {
        if(sieve[i]) {
            dp[i] = dp[sieve[i]] + 1;
        }
        else {
            dp[i] = 1;
            for(int j = 2 * i; j < MAX; j += i) sieve[j] += i;
        }
        for(int j = 0; j < MAX2; j++) sum[i][j] = sum[i - 1][j];
        sum[i][dp[i]]++;
    }
    int p;
    scanf("%d", &p);
    while(p--) {
        int a, b, k;
        scanf("%d%d%d", &a, &b, &k);
        if(k >= MAX2) printf("0\n");
        else printf("%d\n", sum[b][k] - sum[a - 1][k]);
    }
    return 0;
}
```
