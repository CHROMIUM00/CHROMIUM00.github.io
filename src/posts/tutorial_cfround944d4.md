---
title: Codeforces Round 944 (Div. 4) 解题报告
shortTitle: CFround944D4

---

## A // My First Sorting Problem

> [模拟] / CLIST __*361__ / [Link](https://codeforces.com/contest/1971/problem/A) 

```c++
int x, y;
void ChromicChlorideHexahydrate()
{
        cin >> x >> y;
        cout << min(x, y) << " " << max(x, y) << endl;
}
```

## B // Different String

> [模拟] [字符串] / CLIST __*715__ / [Link](https://codeforces.com/contest/1971/problem/B)

从左到右找第一个与之前不同的字母，把这个字母放到前面。

```c++
string s;
void ChromicChlorideHexahydrate()
{
        cin >> s;
        bool same = true;
        char fi = s[0];
        string a, b;
        for (int i = 0; i < s.size(); i++) {
                if (s[i] != fi) {
                        same = false;
                        a = s.substr(0, i);
                        b = s.substr(i);
                        break;
                }
        }
        if (same) {
                cout << "NO" << endl;
        } else {
                cout << "YES" << endl;
                cout << b << a << endl;
        }
}
```

## C // Clock and Strings

> [模拟] / CLIST __*882__ / [Link](https://codeforces.com/contest/1971/problem/C)

枚举所有点记录在红线的左侧还是右侧，之后直接查询 $c$ $d$ 的位置。

```c++
int a, b, c, d;
void ChromicChlorideHexahydrate()
{
        cin >> a >> b >> c >> d;
        set<int> l, r;
        bool swc = 0;
        for (int i = 1; i <= 12; i++) {
                if (i == a || i == b) {
                        swc = !swc;
                        continue;
                }
                if (swc) {
                        l.insert(i);
                } else {
                        r.insert(i);
                }
        }
        if (l.count(c) && l.count(d) || !l.count(c) && !l.count(d)) {
                cout << "NO" << endl;
        } else {
                cout << "YES" << endl;
        }
}
```

## D // Binary Cut

> [贪心] / CLIST __*1088__ / [Link](https://codeforces.com/contest/1971/problem/D)

显然需要将所有连续的 0/1 段切出来才能排序，所以答案就为连续的 0/1 段的数量，例外情况是形如 000111 ，可以将这一段保留不切，此时答案减一。

```c++
string s;
void ChromicChlorideHexahydrate()
{
        cin >> s;
        int cnt = 1;
        bool otoone = false;
        for (int i = 1; i < s.size(); i++) {
                if (s[i] != s[i - 1]) {
                        cnt++;
                }
                if (s[i - 1] == '0' && s[i] == '1') {
                        otoone = true;
                }
        }
        if (otoone) {
                cnt = max(1, cnt - 1);
        }
        cout << cnt << endl;
}
```

## E // Find the Car

> [数学] / CLIST __*1494__ / [Link](https://codeforces.com/contest/1971/problem/E)

简单分数数学题

如果 $d=a_i$ 则直接输出对应的 $b_i$ ，否则：

由于在相邻路牌之间汽车匀速行驶，设当前汽车位置为 $d(a_i<d<a_{i+1})$ ，当前时间为 $e$ ，有

$$
\frac{d-a_i}{a_{i+1}-a_i}=\frac{e-b_i}{b_{i+1}-b{i}} \implies e=\frac{(d-a_i)(b_{i+1}-b_i)}{a_{i+1}-a_i}+b_i
$$

用一个 `lower_bound()` 找到 $a_i$ 之后直接计算即可。

```c++
#define int long long
int n, k, q;
int a[100005];
int b[100005];
vector<pair<int, int>> st;
void ChromicChlorideHexahydrate()
{
        st.clear();
        cin >> n >> k >> q;
        for (int i = 1; i <= k; i++) {
                cin >> a[i];
        }
        for (int i = 1; i <= k; i++) {
                cin >> b[i];
        }
        while (q--) {
                int d;
                cin >> d;
                int pr = lower_bound(a, a + 1 + k, d) - a;
                int pl = pr - 1;
                if (a[pr] == d) {
                        cout << b[pr] << " ";
                        continue;
                }
                int r = a[pr], rs = b[pr];
                int l = a[pl], ls = b[pl];
                cout << ls + ((d - l) * (rs - ls)) / (r - l) << " ";
        }
        cout << endl;
}
```
::: warning 浮点数？

整数能算的题就不要用浮点数。本题出现的最大数（$10^9\times 10^9 = 10^{18}$） 已经超过了 `double` 能保证的精度上限（$10^{15}$）。

:::

::: detail 幽默题面

**rounded down** to the nearest integer = 向下取整

rounded **down to the nearest integer** = 四舍五入

这句话干碎了所有使用翻译软件的选手LMAO

以及整个题面都没有说明 sign $a$ 代表当前位置

:::

## F // Circle Perimeter

> [搜索] / CLIST __*1732__ / [Link](https://codeforces.com/contest/1971/problem/F)

注意到题目有一个很奇怪的限制 $\sum r \leq 10^5$ ，这意味着询问的所有点的个数总和不会超过 $2\pi r \approx 628,319$ 个。

就这几个点？开个一百万的什么东西存着直接开搜！

```c++
#define int long long
const int dir[8][2] = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}, {1, 1}, {-1, 1}, {-1, -1}, {1, -1}};
int r;
int stx, sty;
int cnt = 0;
set<pair<int, int>> vis;
void dfs(int nowx, int nowy)
{
        cnt++;
        vis.insert({nowx, nowy});
        for (int i = 0; i < 8; i++) {
                int nx = nowx + dir[i][0], ny = nowy + dir[i][1];
                if (vis.count({nx, ny})) {
                        continue;
                }
                if (nx == stx && ny == sty) {
                        return;
                }
                int dis = sqrt(nx * nx + ny * ny);
                if (r <= dis && dis < r + 1) {
                        dfs(nx, ny);
                }
        }
}
void ChromicChlorideHexahydrate()
{
        vis.clear();
        cin >> r;
        cnt = 0;
        stx = r, sty = 0;
        dfs(stx, sty);
        cout << cnt << endl;
}
```

## G // XOUR

> [位运算] [排序] / CLIST __*1724__ / [Link](https://codeforces.com/contest/1971/problem/G)

$a_i\oplus a_j < 4$ 意味着两个数的二进制表示中第2位（从0开始）开始的所有位都相同，等价于 $a_i/4=a_j/4$ 。

可以发现只有除以四的结果相同的数可以互相交换且仅能在这几个数之间交换，显然把一组数按升序排序会得到字典序最小，因此：

以该数组为例:

$$ 7 ~6 ~4 ~2 ~1 ~9 ~5 ~3 ~8 $$

1. 将所有数除以4，结果相同的分为一组

$$ \red 7 ~\red 6 ~\red 4 ~\green 2 ~\green 1 ~\blue 9 ~\red 5 ~\green 3 ~\blue 8 $$

2. 给每组数分别按升序排序

$$ \red{4 ~5 ~6 ~7} ~|~ \green{1 ~2 ~3} ~|~ \blue{8 ~9} $$

3. 按照原来的顺序插回原数组

$$ \red 4 ~\red 5 ~\red 6 ~\green 1 ~\green 2 ~\blue 8 ~\red 7 ~\green 3 ~\blue 9 $$

4. 得到答案

用 `map` 乱搞一下把这个东西实现就能过。

```c++
int n;
int a[200005];
map<int, vector<int>> mv;
vector<int> outseq;
map<int, int> pv;
void ChromicChlorideHexahydrate()
{
        mv.clear();
        outseq.clear();
        pv.clear();
        cin >> n;
        for (int i = 1; i <= n; i++) {
                cin >> a[i];
                mv[a[i] / 4].push_back(a[i]);
                outseq.push_back(a[i] / 4);
        }
        for (auto &[_, vec] : mv) {
                sort(vec.begin(), vec.end());
        }
        for (auto it : outseq) {
                cout << mv[it][pv[it]++] << " ";
        }
        cout << endl;
}
```