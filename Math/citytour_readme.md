<h4>
Problem Link: https://www.interviewbit.com/problems/city-tour/</br>
Same Problem: https://codeforces.com/contest/294/problem/C</br>
Codeforces Tutorial Link: https://codeforces.com/blog/entry/7287</br>
<br>
Sample Input:<pre>
11, 2
4, 8
</pre>
Sample Output:<pre>
6720
</pre>
</h4>
<h4>Editorial by Codeforces:</h4>
The third sample is ...#...#... where # is a switched on lamp and . is a switched off lamp. As you can see we have three different types of lights. 
The first three lights (Type A), the 5th to 8th lights (Type B) and the last three lights (Type C). We have to switch on the lights three times for each type of lights. 
Aside from the order of moves for each type there are 9!/(3!*3!*3!) possible permutations of the string AAABBBCCC which tells us how to combine the steps of different types. 
Switching on the lights is done uniquely for types 1 and 3. But for type 2 each time we have to possible options until we're left with one off light. 
So there are 2^3 - 1 ways to do this. So the answer would be 1680*1*4*1 = 6720.

The general solution would be to find all groups off consecutive switched off lamps and calculate the number of ways to combine all these groups. 
Then for each group you should calculate in how many ways it can be solved.

<h4>Editorial By Interviewbit:</h4>
This question is simple if you understand how we are dividing the cities.

Let’s say we start with (a_1, a_2, a_3,…, a_k) as visited cities. Let all cities be (1,2,…,n). Also, let’s denote cities between a_i and a_i+1 as X_i. 
Here, X_0 denotes cities before the first visited city. X_k denotes unvisited cities after a_k.
Now there are total n-k unvisited cities. We can visit them in (n-k)! ways.
We can not visit cities between a_i and a_i+1 in all possible ways for all i’s. We have counted all permutations in (n-k)!. So, we need to divide it with X_i for all i’s.
This will lead to the expression (n-k)!/(X_0! * X_1! * … *X_k!).
There are more than one way of visiting cities between the two visited cities. Precisely, there are 2^(X_i-1) ways to visit for all i. Thus we need to multiply with X_i’s.
Here we need to remember that there is only one way to visit X_0 and X_k.

The final formula becomes ((n-k)!/(X_0!X_2!…X_(k-1)!) )(2^(X_1 -1 + X_2 - 1 + X_3 - 1 + … + X_(k-1)-1)).

The same solution is present as the editorial for this question. You have to remember 
that ((X_0 + X_1)C(X_1))((X_0+X_1+X_3)C(X_3))…((n-k)C(X_k)) is same as ((n-k)!/(X_0!X_2!…*X_(k-1)!)).

<h5>My / Interviewbit Solution : Easy Understandable Solution (Best Solution)</h5>
<h6>
    
```cpp

// Modulo Multiplication that is a*b % mod .
long long multi_mod(long long a, long long b, long long mod) {
    return (a * b) % mod;
}
// Calculate the modulo multiplicative inverse (mmi) of |n|.
// (n * mmi(n)) % mod = 1. (We take 0 < mmi(n) < mod)
// This exists when gcd(n, mod) = 1
long long mmi(long long n, long long mod) {
    long long result = 1, exponent=mod-2;
    while(exponent>0){
        if(exponent & 1){
            result = multi_mod(result, n, mod);
        }
        n = multi_mod(n, n, mod);
        exponent = exponent>>1;
    }
    return result;
}

// Modulo division, done with multiplicative inverse.
long long div_mod(long long a, long long b, long long mod) {
    return (a * mmi(b,mod)) % mod;
}

//To calculate Power = (base^exponent) % mod
long long power(long long base, long long exponent, long long mod){
    if(exponent==0)return 1;
    base = base%mod;
    if(base==0)return 0;
    if(exponent<0){
        base = mmi(base, mod);
        exponent = -exponent;
    }
    long long result = 1;
    while(exponent>0){
        if(exponent & 1){
            result = multi_mod(result, base, mod);
        }
        base = multi_mod(base, base, mod);
        exponent = exponent>>1;
    }
    return result;
}

long long fact(long long n, long long mod) {
    return n<=1 ? 1 : multi_mod(fact(n-1, mod), n, mod);
}

int Solution::solve(int A, vector<int> &B) {
    B.push_back(0);
    B.push_back(A + 1);
    sort(B.begin(), B.end());
    long long mod = 1000000007;
    vector<int> ranges;
    int num_holes = 0;
    int non_zeroes = 0;
    for (int i = 1; i < B.size(); i++) {
        int diff = B[i] - B[i-1];
        if (diff == 0)continue;  // Repeated number. Skip.

        ranges.push_back(diff - 1);
        num_holes += diff - 1;
        if (diff - 1 > 0)non_zeroes++;
    }
    if (ranges.front() > 0)non_zeroes--;
    if (ranges.back() > 0)non_zeroes--;

    // result = (ranges[0], ..., ranges[n-1])! * 
    //              2 ^ (ranges[1] - 1) * ... * 2 ^ (ranges[n-2] - 1)
    // Note: (r_0, r_1, ..., r_n-1)! refers to the multinomial coefficient.
    long long exponent = num_holes - non_zeroes - ranges.front() - ranges.back();
    long long result = multi_mod(fact(num_holes, mod), power(2, exponent, mod), mod);

    for(int i=0; i<ranges.size(); i++){
        result = div_mod(result, fact(ranges[i], mod), mod);
    }
    return (int) result;
}

```

<h5>Codeforces Solution:</h5>

```cpp

#include<cstdio>
#include<iostream>
#include<cstring>
#include<algorithm>
#include<list>
#include<queue>
#define LL long long
#define M 1000000007
using namespace std;
LL n,m,nCr[1001][1001],ans=1,s[1001],pow[1001];
int main(){
    cin >> n >> m;
    for(int i=0;i<=n;i++){
        for(int j=0;j<=i;j++){
            if(j==0 || j==i) nCr[i][j]=1;
            else nCr[i][j]=(nCr[i-1][j-1]+nCr[i-1][j])%M;
        }
    }
    pow[0]=1;pow[1]=1;
    for(int i=2;i<=n;i++) pow[i]=(pow[i-1]*2)%M;
    for(int i=0;i<m;i++) cin >> s[i];
    sort(s,s+m);
    LL cnt=0,tmp;
    for(int i=0;i<m;i++){
        if(i==0) tmp=s[i]-1;
        else{
            tmp=s[i]-s[i-1]-1;
            ans=(ans*pow[tmp])%M;
        }
        cnt+=tmp;
        ans=(ans*nCr[cnt][tmp])%M;
    }
    cnt+=n-s[m-1];
    ans=ans*nCr[cnt][n-s[m-1]]%M;
    cout << ans << endl;
    return 0;
}

```

