---
title: "A - Pay to Win"
date: 2020-03-11T01:03:54+08:00
categories:
  - 剑指offer
tags:
  - 动态规划
---

# 题目

[https://atcoder.jp/contests/agc044/tasks/agc044\_a](https://atcoder.jp/contests/agc044/tasks/agc044_a)

# 综述

# Code

```c++
import java.util.*;

public class Main {
	static HashMap<Long, Long> memo;
	static int A,B,C,D;
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		
		int T = sc.nextInt();
		
		for(int i=0; i<T; i++) {
			long N = sc.nextLong();
			A = sc.nextInt();
			B = sc.nextInt();
			C = sc.nextInt();
			D = sc.nextInt();
			
			memo = new HashMap<>();
			memo.put(0l, 0l);
			memo.put(1l, (long)D);
			
			System.out.println(solve(N));
		}
		
		sc.close();
	}
	
	static long solve(long N) {
		if(memo.get(N)!=null)
			return memo.get(N);
		long min = Long.MAX_VALUE;
		if(N < Long.MAX_VALUE/D)
			min = N*D;
		
		for(int i=-1; i<=1; i++) {
			if((N+i)%2==0)
				min = Math.min(min, solve((N+i)/2)+Math.abs(i)*D+A);
		}
		for(int i=-1; i<=1; i++) {
			if((N+i)%3==0)
				min = Math.min(min, solve((N+i)/3)+Math.abs(i)*D+B);
		}
		for(int i=-2; i<=2; i++) {
			if((N+i)%5==0)
				min = Math.min(min, solve((N+i)/5)+Math.abs(i)*D+C);
		}

		memo.put(N, min);
		return min;
	}
}
```

# 复杂度分析

**时间复杂度：**O(logn)
