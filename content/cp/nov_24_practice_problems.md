+++
title = 'Nov_24_practice_problems'
date = 2024-11-17T00:05:13Z
draft = false
+++

## [CF2037E Kachina's Favorite Binary String](https://codeforces.com/contest/2037/problem/E)

/*
   shame did not solve during competition. Just need to notice that if q(l, r - 1) < q(l, r), then v[r] must be one
   otherwise, v[r] is zero. so n queries can figure out the entire array.
   another thing that need to pay attention is once q(l, r) == 0, v[r - q(l, r + 1) : l] == 0, and anything that comes before that is 1
*/

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <string>
#include <cmath>
#define gi get_int()
using namespace std;
#define int long long
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

const int max_n = 2e5 + 10, mod = 998244353;
int v[max_n], ans[max_n];
int n, m, k;

/*
   shame did not solve during competition. Just need to notice that if q(l, r - 1) < q(l, r), then v[r] must be one
   otherwise, v[r] is zero. so n queries can figure out the entire array.
   another thing that need to pay attention is once q(l, r) == 0, v[r - q(l, r + 1) : l] == 0, and anything that comes before that is 1
*/
void solve()
{
	n = gi;
	int last_known = 2;
	v[1] = 0;
	for (int i = n; i >= 2; i--) {
		cout << "? 1 " << i << endl;
		cout.flush();
		v[i] = gi;
		if (v[i] < v[i + 1]) { // n + 1 doesnt matter
			ans[i + 1] = 1;
		} else {
			ans[i + 1] = 0;
		}
		if (v[i] == 0) {
			if (i == n) {
				cout << "! IMPOSSIBLE" << endl;
				return ;
			}
			int j;
			for (j = i; j > i - v[i + 1]; j--) {
				ans[j] = 0;
			}
			for (; j >= 1; j--) {
				ans[j] = 1;
			}
			cout << "! ";
			for (j = 1; j <= n; j++) {
				cout << ans[j];
			}
			cout << endl;
			return ;
		}
	}
	ans[2] = 1;
	ans[1] = 0;
	cout << "! ";
	for (int j = 1; j <= n; j++) {
		cout << ans[j];
	}
	cout << endl;
}

signed main()
{
	// freopen("code.in", "r", stdin);
	// freopen("code.out", "w", stdout);
	int T = gi;
	while (T--) {
		solve();
	}
	return 0;
}
```

## [CF2037G Natlan Exploring](https://codeforces.com/contest/2037/problem/G)

/*
   A very simple dp is f[i] is the number of path to go to num[i], and can be transitioned from everything before with gcd != 0
   however, this is too slow. to optimize this: take the prefix sum of f[i] with the same prime factors (the multiplicity does not matter), and use inclusion-exclusion rule to remove redundancy.
*/

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <string>
#include <cmath>
#define gi get_int()
using namespace std;
#define int long long
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

const int max_n = 1e6 + 10, mod = 998244353;
int v[max_n], f[max_n], not_prime[max_n];
int n, m, k;
vector<int> prime_numbers;

void factorize(int x, vector<int> &fact)
{
	for (int one_prime = 2; one_prime * one_prime <= x; one_prime++) {
		if (x % one_prime != 0) {
			continue;
		}
		fact.push_back(one_prime);
		while (x % one_prime == 0) {
			x /= one_prime;
		}
	}
	if (x != 1) {
		fact.push_back(x);
	}
}

/*
   A very simple dp is f[i] is the number of path to go to num[i], and can be transitioned from everything before with gcd != 0
   however, this is too slow. to optimize this: take the prefix sum of f[i] with the same prime factors (the multiplicity does not matter), and use inclusion-exclusion rule to remove redundancy.
*/
void solve()
{
	int n = gi;
	for (int i = 0; i < n; i++) {
		v[i] = gi;
	}
	int ans;
	for (int i = 0; i < n; i++) {
		vector<int> factors;
		factorize(v[i], factors);
		ans = 0;
		for (int j = 1; j < (1 << factors.size()); j++) {
			int product = 1, sum = 0;
			for (int k = 0; k < factors.size(); k++) {
				if ((j >> k) & 1) {
					product *= factors[k];
					sum += ((j >> k) & 1);
				}
			}
			if (sum % 2 == 0) {
				ans -= f[product];
				ans += mod;
				ans %= mod;
			} else {
				ans += f[product];
				ans %= mod;	
			}
		}
		/*
		for (int factor : factors) {
			cout << factor << ' ';
		}
		*/
		for (int j = 1; j < (1 << factors.size()); j++) {
			int product = 1;
			for (int k = 0; k < factors.size(); k++) {
				if ((j >> k) & 1) {
					product *= factors[k];
				}
			}
			f[product] += (i == 0 ? 1 : ans);
			f[product] %= mod;
		}
	}
	cout << ans << endl;
}

signed main()
{
	freopen("code.in", "r", stdin);
	// freopen("code.out", "w", stdout);
	for (int i = 2; i <= max_n - 10; i++) {
		if (not_prime[i] == 1) continue;
		for (int j = 2; j * i <= max_n - 10; j++) {
			not_prime[j * i] = 1;
		}
	}
	for (int i = 2; i <= 1000; i++) {
		if (not_prime[i] == 0) {
			prime_numbers.push_back(i);
		}
	}
	int T = 1;
	while (T--) {
		solve();
	}
	return 0;
}

```

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

## [CF2030E MEXimize the Score](https://codeforces.com/contest/2030/problem/E)

/*
   Formalize the question: for a given subsequence, the best possible subset arrangement is [0, 1, 2, ...], [0, ...], in increasing order.
   Need to notice: the contribution of a subset is the length of the longest continuous prefix from 0. anything after the prefix does not matter. 
   Therefore, we can view it as that each element in the longest continous prefix contribute 1 to the final answer.
   Now look at all possible combinations: we don't look at indivdual numbers, we look at the total number of a specific number, total_cnt[x]
   For some x with cnt[x] (cnt[x] < total_cnt[x]) to make contribution to the final answer, we need to put them in a subseuqnce such that all numbers that comes before x have a cnt larger than cnt[x]
   We can make some very complicated combinatorics here, or we make can make a dp to make the problem simpler:
   f[i][j] represent the number of subsequence such that we only use numbers <= i, and the smallest cnt occurence of a number is j. therefore the contribution of number i with cnt of j can be counted toward the final answer
   we can transition this in 2 ways:
		- the current number i occurs j times -> we've set the min value of the subsequence, so all other numbers before i only need to occur more than j + 1 times (we handle j times in the second case) -> can be transitioned from f[i - 1][k | k > j && k < reasonable_cnt]
		- the current number i occurs more than j times, so we need to have some value before i occur j times (must) -> can be transitioned from f[i - 1][j]
	there are some other implementation details that need to be considered.
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
#define int long long
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

const int max_n = 2e5 + 10, mod = 998244353;
int v[max_n], f[max_n], fact[max_n], ifact[max_n], pre_c[max_n], pre_f[max_n];

int C(int n, int m)
{
	if (m > n) {
		return 0;
	}
	return (fact[n] * ifact[m] % mod) * ifact[n - m] % mod;
}

int qpow(int x, int p)
{
	int ans = 1, base = x;
	while (p != 0) {
		if (p & 1) {
			ans = ans * base % mod;
		}
		base = base * base % mod;
		p >>= 1;
	}
	return ans;
}

/*
   Formalize the question: for a given subsequence, the best possible subset arrangement is [0, 1, 2, ...], [0, ...], in increasing order.
   Need to notice: the contribution of a subset is the length of the longest continuous prefix from 0. anything after the prefix does not matter. 
   Therefore, we can view it as that each element in the longest continous prefix contribute 1 to the final answer.
   Now look at all possible combinations: we don't look at indivdual numbers, we look at the total number of a specific number, total_cnt[x]
   For some x with cnt[x] (cnt[x] < total_cnt[x]) to make contribution to the final answer, we need to put them in a subseuqnce such that all numbers that comes before x have a cnt larger than cnt[x]
   We can make some very complicated combinatorics here, or we make can make a dp to make the problem simpler:
   f[i][j] represent the number of subsequence such that we only use numbers <= i, and the smallest cnt occurence of a number is j. therefore the contribution of number i with cnt of j can be counted toward the final answer
   we can transition this in 2 ways:
		- the current number i occurs j times -> we've set the min value of the subsequence, so all other numbers before i only need to occur more than j + 1 times (we handle j times in the second case) -> can be transitioned from f[i - 1][k | k > j && k < reasonable_cnt]
		- the current number i occurs more than j times, so we need to have some value before i occur j times (must) -> can be transitioned from f[i - 1][j]
	there are some other implementation details that need to be considered.
*/
void solve()
{
	int n = gi;
	for (int i = 0; i < n; i++) {
		v[i] = 0;
		f[i] = 0;
	}
	for (int i = 0; i < n; i++) {
		v[gi] += 1;
	}
	int ans = 0, remain = n - v[0];
	for (int i = 1; i <= v[0]; i++) {
		f[i] = C(v[0], i);
		ans = (ans + f[i] * qpow(2, remain) % mod * i) % mod;
	}
	for (int i = 1; i < n; i++) {
		remain -= v[i];
		pre_c[1] = C(v[i], 1);
		for (int j = 2; j <= v[i]; j++) {
			pre_c[j] = (pre_c[j - 1] + C(v[i], j)) % mod;
		}
		pre_f[1] = f[1];
		for (int j = 2; j <= v[i - 1]; j++) {
			pre_f[j] = (pre_f[j - 1] + f[j]) % mod;
		}
		for (int j = 1; j <= v[i]; j++) {
			int tmp1 = (pre_f[v[i - 1]] - pre_f[j] + mod) % mod;
			tmp1 = (tmp1 * C(v[i], j)) % mod;
			int tmp2 = (pre_c[v[i]] - pre_c[j - 1] + mod) % mod;
			tmp2 = (tmp2 * f[j]) % mod;
			f[j] = (tmp1 + tmp2) % mod;
			if (v[i - 1] < j) f[j] = 0;
			ans = (ans + f[j] * qpow(2, remain) % mod * j % mod) % mod;
		}
	}
	cout << ans << endl;
}

void prep() 
{
	ifact[0] = 1;
	fact[1] = 1;
	for (int i = 2; i < max_n; i++) {
		fact[i] = fact[i - 1] * i % mod;
	}
	for (int i = 1; i < max_n; i++) {
		ifact[i] = qpow(fact[i], mod - 2);
	}
}

signed main()
{
	freopen("code.in", "r", stdin);
	// freopen("code.out", "w", stdout);
	prep();
	int T = gi;
	while (T--) {
		solve();
	}
	return 0;
}
```
