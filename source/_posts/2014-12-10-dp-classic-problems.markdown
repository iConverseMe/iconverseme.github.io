---
layout: post
title: "DP classic problems, LIS & LCS"
date: 2014-12-10 17:13:52 +0800
comments: true
categories: 
---
**Longest Increasing Substring最长递增子串**
Give a array of numbers, find the longest increasing numbers in the array, and they are continuous in the array.

use DP solution, define a array int[] dp, and dp[i] means the longest increasing substring length end with num[i], and there exists:
```
dp[i] = dp[i-1] + 1, if num[i] > num[i-1]
dp[i] = 1, if num[i] <= num[i-1]
```
If we want to print the nums, we can record the last index of the sub array. Here is the code:
```
// dp[i] means end with index i, the length of LIS
	// dp[i] = dp[i-1]+1, num[i] > num[i-1]
	// dp[i] = 1, num[i] <= num[i-1]
	public String longestIncresingSubstring(int[] num) {
		int len = num.length;
		int[] dp = new int[len];
		if(len == 0) return "";
		dp[0] = 1;
		int max = 1;
		int endIndex = 0;
		
		for(int i = 1; i < len; i ++) {
			dp[i] = 1;
			if(num[i] > num[i-1]) {
				dp[i] += dp[i-1];
			}

			if(max < dp[i]) {
				max = dp[i];
				endIndex = i;
			}
		}

		System.out.println("longest incresing substring's length: " + max);
		StringBuffer sb = new StringBuffer();
		for(int i = endIndex - max + 1; i <= endIndex; i ++) {
			sb.append(num[i]).append(",");
		}
		return sb.toString();
	}
```

**Longest Increasing Subsequence最长递增子序列**
```
// dp[i] = 1 + max{dp[j], 0 <= j < i && A[i] > A[j]}
```
Use a map to record the pre index of each index
```
public List<Integer> longestIncresingSubsequance(int[] num) {
		int len = num.length;
		int[] dp = new int[len];
		int max = 1;
		dp[0] = 1;
		
		int maxEndIndex = 0;
		Map<Integer, Integer> preIndexMap = new HashMap<Integer, Integer>();
		// init the map, set pre index to be itself for each index
		for(int i = 0; i < len; i ++) {
			preIndexMap.put(i, i);
		}

		for(int i = 1; i < len; i ++) {
			dp[i] = 1;
			int tmpMax = 0;
			for(int j = 0; j < i ; j++) {
				if(num[i] > num[j]) {
					if(dp[j] > tmpMax) {
						tmpMax = dp[j];
						preIndexMap.put(i, j);
					}
				}
			}
			dp[i] += tmpMax;
			
			if(max < dp[i]) {
				max = dp[i];
				maxEndIndex = i;
			}
		}
		
		System.out.println("longest incresing subsequence's length: " + max);
		// for maxEndIndex start, total max nums
		List<Integer> list = new ArrayList<Integer>();
		while(max > 0) {
			list.add(num[maxEndIndex]);
			maxEndIndex = preIndexMap.get(maxEndIndex);
			max--;
		}
		Collections.reverse(list);
		return list;
	}
```


**Longest Common Substring最长公共子串**

Find the longest common substring of two strings s1 and s2, and the substring should be continuous in s1 or s2. define a array int[][] dp, and dp[i][j] means the length of LCS for s1[0...i] and s2[0...j]. So there exists:
```
if s[i] == t[j], dp[i][j] = dp[i-1][j-1]+1
if s[i] != t[j], dp[i][j] = 0
```
And the result is the max of dp[i][j].

If we want to print out the substring, we need a index to record the last index of the substring. Here is the code:
```
public String getLongestCommonSubstring(String s1, String s2) {
		int maxLen = 0;
		int endIndex = 0;
		int len1 = s1.length();
		int len2 = s2.length();
		char[] c1 = s1.toCharArray();
		char[] c2 = s2.toCharArray();

		int[][] dp = new int[len1+1][len2+1];
		for(int i = 0; i < len1; i ++) {
			for(int j = 0; j < len2; j ++) {
				if(c1[i] == c2[j]) {
					dp[i+1][j+1] = dp[i][j] + 1;
				} else {
					dp[i+1][j+1] = 0;
				}
				if(maxLen < dp[i+1][j+1]) {
					maxLen = dp[i+1][j+1];
					endIndex = i;
				}
			}
		}

		System.out.println("longest common substring: " + maxLen);
		return s1.substring(endIndex-maxLen+1, endIndex+1);
	}
```

**Longest Common Subsequence最长公共子序列**
This one is diff from the above, the result can be in-continuous in s or t. Also use dp[i][j] to describe the length of LCS in s[0...i] and t[0...j]. And there exists:
1. when s[i] == t[j], LCS(s[0...i] & t[0...j]) = LCS( s[0...i-1] & t[0...j-1] ) + s[i]
2. when s[i] != t[j], LCS(s[0...i] & t[0...j]) = LCS( s[0...i-1] & t[0...j]) OR LCS( s[0...i] & t[0...j-1])
	so in this case, dp[i][j] = max{dp[i-1][j], dp[i][j-1]}
```
if s[i] == t[j], dp[i][j] = dp[i-1][j-1]+1
if s[i] != t[j], dp[i][j] = max{dp[i-1][j], dp[i][j-1]}
```
And we need list dp array to find the sub sequence, here is the code:
```
public String getLongestCommonSubsequence(String s1, String s2) {
		int len1 = s1.length();
		int len2 = s2.length();
		char[] c1 = s1.toCharArray();
		char[] c2 = s2.toCharArray();

		int[][] dp = new int[len1+1][len2+1];
		for(int i = 0; i < len1; i ++) {
			for(int j = 0; j < len2; j ++) {
				if(c1[i] == c2[j]) {
					dp[i+1][j+1] = dp[i][j] + 1;
				} else {
					dp[i+1][j+1] = Math.max(dp[i+1][j], dp[i][j+1]);
				}
			}
		}

		int lcsMaxLength = dp[len1][len2];
		System.out.println("longest common subsequence: " + lcsMaxLength);

		StringBuffer sb = new StringBuffer();
		int index1 = len1;
		int index2 = len2;
		while(index1 > 0 && index2 > 0) {
			if(c1[index1-1] == c2[index2-1]) {
				sb.append(c1[index1-1]);
				index1 --;
				index2 --;
			} else if(dp[index1][index2-1] >= dp[index1-1][index2]) {
				index2 --;
			} else {
				index1 --;
			}
		} 
		return sb.reverse().toString();
	}	
```

