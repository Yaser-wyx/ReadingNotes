# Wannafly挑战赛9 C 列一列

[TOC]

**原题地址：https://www.nowcoder.com/acm/contest/71/C**

## 思路

一道求斐波那契数列第K项的题目。一开始的想法是直接打表打到100000，然后用二分查找来找位置，但突然发现我太天真了，别说第100000位的斐波那契数，哪怕是第1000位都已经精度溢出了，所以只能用hash，在打表的时候用一个很大的素数对每一次相加进行取模运算，这样就可以保证不会出现精度溢出了，且基本不会出现取模运算后相同的结果。

## AC代码

```java
import java.io.BufferedInputStream;
import java.math.BigInteger;
import java.util.Scanner;

/**
 * Created with IntelliJ IDEA.
 *
 * @author wanyu
 * @Date: 2018-02-13
 * @Time: 22:33
 * To change this template use File | Settings | File Templates.
 * @desc
 */
public class Main {

    private static long mod = 177777777;//使用一个极大的素数用来取模运算
    private static long[] nums = new long[100010];

    public static void main(String[] args) {
        Scanner in = new Scanner(new BufferedInputStream(System.in));
        fab();
        while (in.hasNext()) {
            String s = in.nextLine();
            BigInteger temp = new BigInteger(s);
            int n = temp.mod(BigInteger.valueOf(mod)).intValue();
            for (int i = 1; i < nums.length; i++) {
                if (nums[i] == n) {
                        System.out.println(i);
                    break;
                }
            }
        }
    }

    private static void fab() {
        nums[0] = 1;
        nums[1] = 1;
        for (int i = 2; i <= 100000; i++) {
            nums[i] = (nums[i - 2] + nums[i - 1]) % mod;//取模运算
        }
    }
}

```