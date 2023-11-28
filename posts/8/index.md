# C++에서 참조자(Reference) 구현에 대한 궁금증

<!--more-->

# 배경
"윤성우의 열혈 C++" 책을 보면서 C++에 대한 공부를 하던 중<br>참조자에 대해 공부하였는데 참조자는 어떻게 구현되어있는지 궁금하여 IDA로 리버싱 해본다.

# 본론
## 참조자(Reference)

참조자는 메모리 공간에 이름을 하나 더 붙여주는 방법이다. 기존에 있는 변수명 외에도 별칭을 부여하여 사용 가능하다.

```Cpp
int num1 = 1337;
int &num = num1;
```

이렇게 사용한다.

```cpp
//Reference.cpp
#include <iostream>
using namespace std;

int main(void)
{
    int num1 = 1020;
    int &num2 = num1;

    cout << "VAL: " << num1 << endl;
    cout << "REF: " << num2 << endl;
    num2 = 3047;
    cout << "VAL: " << &num1 << endl;
    cout << "REF: " << &num2 << endl;
    return 0;
}
```

참조자를 설명하기 위한 코드이며, 출력 결과는 다음과 같다.

```
VAL: 1020
REF: 1020
VAL: 0x61ff08
REF: 0x61ff08
```
아예 같은 주소를 가리키는 것을 볼 수 있다.


## IDA 분석

```cpp
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v3; // eax
  int v4; // eax
  int v5; // ebx
  int v6; // eax
  int v7; // eax
  int v8; // eax
  int v9; // eax
  int v10; // eax
  int v11; // eax
  int num1; // [esp+0h] [ebp-10h] BYREF
  int *num2; // [esp+4h] [ebp-Ch]
  int *p_argc; // [esp+8h] [ebp-8h]

  p_argc = &argc;
  __main();
  num1 = 1020;
  num2 = &num1;
  v3 = std::operator<<<std::char_traits<char>>(&std::cout, "VAL: ");
  v4 = std::ostream::operator<<(v3, 1020);
  std::ostream::operator<<(v4, &std::endl<char,std::char_traits<char>>);
  v5 = *num2;
  v6 = std::operator<<<std::char_traits<char>>(&std::cout, "REF: ");
  v7 = std::ostream::operator<<(v6, v5);
  std::ostream::operator<<(v7, &std::endl<char,std::char_traits<char>>);
  *num2 = 3047;
  v8 = std::operator<<<std::char_traits<char>>(&std::cout, "VAL: ");
  v9 = std::ostream::operator<<(v8, &num1);
  std::ostream::operator<<(v9, &std::endl<char,std::char_traits<char>>);
  v10 = std::operator<<<std::char_traits<char>>(&std::cout, "REF: ");
  v11 = std::ostream::operator<<(v10, num2);
  std::ostream::operator<<(v11, &std::endl<char,std::char_traits<char>>);
  return 0;
}
```

해당 코드를 IDA로 분석했을 때, main 함수이다.<br>윈도우 환경에서 MinGW로 컴파일되었다.<br>num1에 해당하는 변수에 num1, num2인 변수에 num2로 이름지었다.

C++을 디버깅하는게 처음이라 잘 모르겠지만 차근차근 살펴보자.

24행에 bp를 걸고 어떤 부분에서 출력되는 bp가 있는지 살펴보면 25행에서 출력한다.

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/75ef5afd-4f6d-476e-8346-ee27cce9807a)

25는 ostrema::operator로 되어있는데 이게 머지 25, 25, 26 구조를 보면 각각의 문자열이 이어지는 과정을 각 변수에 넣으면서 std::ostream::operator로 이어가는 것 같다. (REF, v5, endl)

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/a80451dc-32a6-4f5c-b13c-df8b4250e9a3)

그냥 찾아보긴 하는데 얘네들인가 싶다.

어찌되었던, v5가 출력할 때 사용된 reference이고, 이는 int형 변수이지만 *num2를 가진다. num2는 &num1로

내부에서는 포인터를 통해 주소를 지정한다.


```cpp
  v8 = std::operator<<<std::char_traits<char>>(&std::cout, "VAL: ");
  v9 = std::ostream::operator<<(v8, &num1);
  std::ostream::operator<<(v9, &std::endl<char,std::char_traits<char>>);
  v10 = std::operator<<<std::char_traits<char>>(&std::cout, "REF: ");
  v11 = std::ostream::operator<<(v10, num2);
  std::ostream::operator<<(v11, &std::endl<char,std::char_traits<char>>);
```

주소 출력 시에는 num2를 그대로 출력한다.

# 결론
생성자는 내부에서 포인터로 구현되며 사용자의 편의를 위해서 보이는 것만 다르다.

새로 알게 된 점은 C++을 정적 디버깅해보면서 cout 코드 보는 법을 알았다.

