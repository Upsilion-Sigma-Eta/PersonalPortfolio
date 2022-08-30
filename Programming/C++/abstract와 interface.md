# abstract와 interface의 차이와 공통점, 그리고 C++에서의 적용

Java의 경우 추상 클래스와 인터페이스는 구분되어 있으며, [표 1]과 같은 특징을 가진다[^1]

|               인터페이스                |                         추상 클래스                          |
| :-------------------------------------: | :----------------------------------------------------------: |
|      객체의 사용 방법을 정의한다.       |                    상속을 위해 사용한다.                     |
|         함수의 구현을 강제한다.         |                    추상 메소드를 가진다.                     |
| class 대신 interface 키워드를 사용한다. |       abstract 키워드 사용, new를 통해 객체 생성 불가        |
|        상수와 메소드만을 가진다.        | abstract 메소드는 자식 클래스에서 반드시 구현해야 한다. (오버라이딩) |

<p align="center"/>[표 1] 자바에서의 abstract와 interface의 특징

하지만 C++에서는 interface 키워드가 존재하지 않는다. 그렇다면 C++에서는 interface를 어떻게 구현할까?

C++는 가상 소멸자와 순수 가상 함수를 이용하여 인터페이스처럼 이용한다. 가상 소멸자와 순수 가상 함수는 다음과 같이 정의할 수 있다.

```c++
class dummyInterface
{
    public:
    	virtual ~dummyInterface = default;
    	// 아래의 세 표현은 동일하다.
    	virtual void func_1() = 0;
    	virtual void func_2() = NULL;
    	virtual void func_3() abstract;
}
```

C++에서 순수 가상 함수가 하나라도 있다면 추상 클래스가 된다. 순수 가상 함수를 가지면 인스턴스를 생성할 수 없기 때문이다. 또한 순수 가상함수를 가진 클래스를 상속받은 자식 클래스들은 반드시 순수 가상 함수를 재정의하지 않고 인스턴스를 만들어도 컴파일 에러가 발생한다[^1] 순수 가상함수는 선언만 하고 정의를 하지 않는다. C++에서 순수 가상함수를 하나라도 가지는 클래스는 추상 클래스로 정의된다.

추상 클래스의 인스턴스는 해당 추상클래스를 상속해서 가상 함수를 구현한 자식 클래스를 통해서 인스턴스를 생성할 수 있다[^2]. 즉, 아래와 같은 사용법이 성립한다.

```c++
#include <iostream>

class abstractClass abstract
{
    public:
        virtual ~abstractClass() = default;
        virtual void func_1() abstract;
        virtual void func_2() abstract;
};

class actualClass : public abstractClass
{
    public:
        void func_1() override {
            std::cout << "func_1 out" << std::endl;
        }
        void func_2() override {
            std::cout << "func_2 out" << std::endl;
        }
};

int main( void )
{
    abstractClass *abClass = new actualClass();
    abClass->func_1();
    abClass->func_2();

    delete abClass;

    return 0;
}
```

위 코드의 실행 결과는 아래와 같다.

```
func_1 out
func_2 out
```

위 코드에서 주의해야하는 부분은 가상 함수를 선언할 때 뒤쪽에 abstract 키워드를 붙여줘야 한다는 것이다. 만약에 abstract 키워드가 생략되면 Visual Studio 2022 Preview 기준으로 LNK1120 에러를 띄우면서 컴파일 되지 않는다. 가상 소멸자 쪽에는 `= default`를 붙여줘야 한다.

'가상 소멸자'란 것이 등장했는데, [^2]에서는 가상 소멸자의 필요성을 다음과 같이 말하고 있다.

> 즉, 동적 할당한 부모 class를 메모리 해제 하면서 자식 class의 소멸자를 호출해야 하는데 이것이 안되고 단순히 부모 class의 소멸자만 호출되고 있습니다.
>
> …
>
> 위 코드 처럼 소멸자에 virtual 키워드를 추가하여 가상 소멸자 형태를 취하면 부모 class가 소멸자를 호출하면서 자식class의 소멸자를 호출하게 하여 문제를 해결하게 됩니다.



[^1]: 인터페이스 vs 추상 클래스, 피로물든딸기의 라이브러리, https://bloodstrawberry.tistory.com/515, Accessed on: 2022-08-29 17:12:23
[^2]: [C++]순수 가상함수와 추상 클래스, 인터페이스(interface), 서정오, 18:11 January 30, 2018, Accessed on: 2022-08-29 17:33:40 

