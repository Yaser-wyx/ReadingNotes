# Wannafly挑战赛9 A 找一找

[TOC]

**原题地址：https://www.nowcoder.com/acm/contest/71/A**

## 思路

使用埃氏筛法来判断第i个数是否有它的倍数，如果有，则找到一个符合条件的数，同时跳出循环，开始判断第i+1个数。

## AC代码

```java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.StreamTokenizer;

/**
 * Created with IntelliJ IDEA.
 *
 * @author wanyu
 * @Date: 2018-02-13
 * @Time: 21:42
 * To change this template use File | Settings | File Templates.
 * @desc
 */
public class Main {
    public static void main(String[] args) throws IOException {
        StreamTokenizer in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
        in.nextToken();
        int n = (int) in.nval;
        int[] nums = new int[n + 1];
        int max = 0;
        boolean[] hash = new boolean[n + 1];//保存该数是否存在
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            nums[i] = (int) in.nval;
            max = Math.max(max, nums[i]);
            hash[nums[i]] = true;//如果存在则将对应的位置设置为true
        }

        int sum = 0;//保存结果
        for (int i = 1; i <= n; i++) {
            if (nums[i] == 1)
                sum++;
            else
                for (int j = 2 * nums[i]; j <= max; j += nums[i]) {//埃氏筛法
                    if (hash[j]) {//存在num[i]的倍数
                        sum++;
                        break;
                    }
                }
        }
        System.out.println(sum);
    }
}

```