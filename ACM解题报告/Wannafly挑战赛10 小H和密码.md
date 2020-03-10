# Wannafly挑战赛10 小H和密码 （DP）

[TOC]

**原题地址：https://www.nowcoder.com/acm/contest/72/B**

## 思路

一开始用的DFS很明显过不去，后来看大佬的代码，原来是动态规划，另外这题的数据太水了，不少代码连样例都过不去却可以AC，我也是服了ORZ。。。。。。

分别用一个二维数组map和一个一维数组empty来表示密码盘，map存的是第i个密码盘上有哪些字母，empty表示第i个密码盘是否有#，即空字符。

再设置一个二维数组dp用来表示代表前i个转盘能否匹配询问中的第j个字母。

如果我们能够在第i个密码盘上匹配到第j个字母则到下一个密码盘去匹配第j+1个字母，或者如果第i个密码盘有空字符`#`，则到第i+1个密码盘去匹配第j个字母。

## AC代码

```c++
#include <bits/stdc++.h>
using namespace std;

/**
 *  Created with IntelliJ Clion.
 *  @author  wanyu
 *  @Date: 2018-02-24
 *  @Time: 12:11
 *  To change this template use File | Settings | File Templates.
 *
 */
int N, M, Q;
bool dial[310][310];//存放整张转盘
bool empty[310];//表示哪一个转盘有#
int dp[310][310];//代表前i个转盘能否找到询问中的第j个字母

int main() {
    scanf("%d %d %d", &N, &M, &Q);
    memset(dial, false, sizeof(dial));
    memset(empty, false, sizeof(empty));
    char temp[M];
    for (int i = 0; i < N; i++) {
        scanf("%s", temp);//存放每个转盘有哪些字母
        for (int j = 0; j < M; j++) {
            dial[i][temp[j]] = true;//第i个转盘对应字母,使用bool表示
            if (temp[j] > 'z' || temp[j] < 'a') {
                empty[i] = true;//如果的i个转盘有#，则代表此行可以为空
            }
        }
    }
    int times = 0;//第几次询问(除去长度过长的)
    while (Q--) {
        char ans[10000];
        scanf("%s", ans);
        auto len = strlen(ans);
        if (len > N) {//长度过长，直接跳过
            puts("NO");
            continue;
        }
        times++;
        dp[0][0] = times;//初始化
        for (int i = 0; i <= N; i++) {
            for (int j = 0; j <= len; j++) {
                if (dp[i][j] != times) {//如果没有标记表明前i个转盘还没有匹配到第j个字母
                    continue;
                }
                if (dial[i][ans[j]]) {
                    //在第i个转盘上匹配到了猜的密码的第j位
                    dp[i + 1][j + 1] = times;//表示下一次从第i+1个转盘开始匹配第j+1个字母
                }

                if (empty[i]) {//如果第i行有空格，则表示本行可以跳过，即不匹配
                    dp[i + 1][j] = times;//表示下一次从第i+1个转盘开始匹配第j个字母
                }
            }
        }
        puts(dp[N][len] == times ? "YES" : "NO");

    }
    return 0;
}
```