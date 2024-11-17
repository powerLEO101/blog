+++
title = 'Nov_24_practice_problems'
date = 2024-11-17T00:05:13Z
draft = false
+++

## [CF2030D QED's Favorite Permutation](https://codeforces.com/contest/2030/problem/D)
/*
   RL can be convert into 0/1 arrays. which will create multiple blocks. number cannot move past a block that has LR.
   if a number need to move from one block to another, its imposible. otherwise the movement within blocks are free
   becuase p never change, maintain an array which describes whether there are numbers that need to flow through place i.
   if one modification removed the blockage, check whether there are numbers that need to flow through that blockage, and, if yes, 
   whether all numbers can go to their desired place.
   if one modiciation add a blockage, check whether numbers need to flow through it, if yes, this will result in a fail
*/

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <string>
#define gi get_int()
using namespace std;
int get_int()
{
	int x = 0, y = 1;
	char ch = getchar();
	while (!isdigit(ch) && ch != '-')
		ch = getchar();
	if (ch == '-')
		 y = -1, ch = getchar();
	while (isdigit(ch))
		x = x * 10 + ch - '0', ch = getchar();
	return x * y;
}

const int max_n = 2e5 + 10;
int s[max_n], v[max_n], sum[max_n], is_bad[max_n];

/*
   RL can be convert into 0/1 arrays. which will create multiple blocks. number cannot move past a block that has LR.
   if a number need to move from one block to another, its imposible. otherwise the movement within blocks are free
   becuase p never change, maintain an array which describes whether there are numbers that need to flow through place i.
   if one modification removed the blockage, check whether there are numbers that need to flow through that blockage, and, if yes, 
   whether all numbers can go to their desired place.
   if one modiciation add a blockage, check whether numbers need to flow through it, if yes, this will result in a fail
*/
void solve()
{
	int n = gi, Q = gi;
	for (int i = 0; i < n; i++) {
		v[i] = gi - 1;
		sum[i] = is_bad[i] = 0;
	}
	for (int i = 0; i < n; i++) {
		char ch;
		cin >> ch;
		s[i] = ch == 'L' ? 0 : 1;
	}
	for (int i = 0; i < n; i++) {
		int start, end;
		start = min(i, v[i]);
		end = max(i, v[i]);
		sum[end] -= 1;
		sum[start] += 1;
	}
	int n_bad = 0;
	for (int i = 1; i < n; i++) {
		sum[i] += sum[i - 1];
		if ((sum[i] != 0) && (s[i] == 0) && (s[i + 1] == 1)) {
			is_bad[i] = true;
			n_bad += 1;
		}
	}
	while (Q--) {
		int x = gi - 1;
		s[x] ^= 1;
		if (s[x] == 0) {
			if (s[x - 1] == 0 && is_bad[x - 1]) {
				is_bad[x - 1] = false;
				n_bad -= 1;
			}
			if (s[x + 1] == 1 && sum[x] != 0) {
				is_bad[x] = true;
				n_bad += 1;
			}
		} else {
			if (s[x + 1] == 1 && is_bad[x]) {
				is_bad[x] = false;
				n_bad -= 1;
			}
			if (s[x - 1] == 0 && sum[x - 1] != 0) {
				is_bad[x - 1] = true;
				n_bad += 1;
			}
		}
		cout << (n_bad == 0 ? "YES" : "NO") << endl;
	}
}

int main()
{
	freopen("code.in", "r", stdin);
	// freopen("code.out", "w", stdout);
	int T = gi;
	while (T--) {
		solve();
	}
	return 0;
}
```