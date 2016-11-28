---
layout: post
title: Aquecimento Final Brasileira
categories: treinos
---

Na véspera da final, resolvemos rever alguns problemas das brasileiras como forma de aquecimento.

O problema escolhido por mim foi o [Problema K (Keep It Energized)](https://icpcarchive.ecs.baylor.edu/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=5225), um dos mais difíceis da final do ano passado.

A solução é bem elaborada, envolvendo um pré-processamento com buscas binárias, depois uma programação dinâmica com otimização.

Era necessário também uma estrutura de dados que permitisse query de mínimo em O(lgN).

Escolhi minha estrutura de dados favorita: Fenwick Tree / Binary Indexed Tree.

```c++
#include <bits/stdc++.h>
#define pb push_back
#define mp make_pair
#define ff first
#define ss second
#define debug(args...) //fprintf(stderr, args)

using namespace std;

typedef long long lint;
typedef pair<int, int> pii;

const int MAXN = 112345;

int dp[MAXN], bit[MAXN], e[MAXN];
vector<pii> s[MAXN];
vector<int> pre[MAXN];
int n, m;

int query(int idx) {
    int ret = INT_MAX;
    for(int i = idx; i > 0; i -= i & -i) ret = min(ret, bit[i]);
    return ret;
}

void update(int idx, int val) {
    for(int i = idx; i <= n + 1; i += i & -i) bit[i] = min(bit[i], val);
}

int main() {
    while(scanf("%d%d", &n, &m) != EOF) {
        e[0] = 0;
        for(int i = 1; i <= n; i++) {
            scanf("%d", &e[i]);
            e[i] += e[i - 1];
            dp[i] = bit[i] = INT_MAX;
            s[i].clear();
            pre[i].clear();
            debug("e[%d] = %d\n", i, e[i]);
        }
        dp[n + 1] = 0;
        bit[n + 1] = INT_MAX;
        for(int i = 1; i <= m; i++) {
            int l, a, b;
            scanf("%d", &l);
            scanf("%d%d", &a, &b);
            s[l].pb(mp(a, b));
        }
        for(int i = 1; i <= n; i++) {
            for(pii p : s[i]) {
                int ub = upper_bound(e, e + (n + 1), e[i - 1] + p.ff) - e;
                pre[i].pb(ub);
                debug("pre[%d] = %d\n", i, ub);
            }
        }
        update(n + 1, 0);
        for(int i = n; i >= 1; i--) {
            for(int j = 0; j < pre[i].size(); j++) {
                int aux = query(pre[i][j]);
                if(aux != INT_MAX) aux += s[i][j].ss;
                dp[i] = min(dp[i], aux);
            }
            update(i, dp[i]);
            debug("dp[%d] = %d\n", i, dp[i]);
        }
        if(dp[1] == INT_MAX) printf("-1\n");
        else printf("%d\n", dp[1]);
    }
    return 0;
}

```