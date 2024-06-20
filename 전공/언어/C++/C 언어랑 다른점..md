~~~cpp
#include <iostream>
#include <bits/stdc++.h>// 모든 헤더파일을 포함하는 헤더파일.
using namespace std;
int main()
{
 ios_base::sync_with_stdio(0);
    cin.tie(0); // 입출력 속도 업 (코테말고 에선 신중히 쓸것.)
	cin >> a; //a 입력 
	cout << a; //a 출력
	for(int i = 0 ; i < 10 ; i++)
	{
		cout << a[i] // 배열출력
	} 
	
}

~~~


문자열 공백까지 읽

``` C++
getline(cin, str); // 개행("\n)이전까지의 문자열 입력받음
getline(cin,str,'a'); // 'a'문자 이전까지의 문자열을 입력받는다.
```