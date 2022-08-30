# C++에서의 상속

본 글의 내용은[^1][^2]에서 많은 내용을 참조하였다.



## 1. 상속의 필요성

C++에서 클래스는 구조체와 유사한 점이 많다. C++에서 구조체는 함수와 변수를 가질 수 있고, 상속할 수도 있다. 구조체와 클래스의 가장 큰 차이점은 별도의 선언이 없는 한 C++에서 구조체의 멤버들은 별도의 접근제어 지시자가 없으면 public으로 설정되고, 클래스에서는 private로 설정된다는 것이다[^3].  C에서의 구조체와는 확연히 다르다. C에서의 구조체는 함수를 묶을 수 없었고, 클래스는 언어 자체적으로 지원하는 대상이 아니었다. C++에서 클래스와 클래스와 유사한 구조체를 지원하게 되면서 '상속'이라는 개념이 도입되었다. 그렇다면 상속은 왜 필요할까?

상속이란 기존 클래스의 변수와 메소드를 물려 받아 새로운 클래스를 구성하는 것을 의미한다[^4]. 상속은 기존 클래스의 변수와 코드를 재사용할 수 있어 개발시간이 단축되고, 먼저 작성된 검증된 프로그램을 재사용하기 때문에 신뢰성 있는 프로그램을 개발할 수 있으며, 클래스 간 계층적 분류 및 관리가 가능하여 유지보수가 용이하다는 장점 때문에 상속이 필요하다고 할 수 있다[^4].

또한 상속은 is-a 관계처럼 계층 구조로 존재하는 대상을 모델링하기에 편리한 방법이다. 물론 상속을 하지 않고 하나의 부모 클래스를 복사해서 다시 자식 클래스를 만드는 식으로 작업할수도 있다. 하지만 이 방법은 단점이 존재하는데[^1],

1. 원본과 새 클래스의 코드는 완전히 독립 되어 있기 때문에, 원본에서 발생한 오류를 수정하려고 하면 새 클래스에서 공유하는 코드들도 전부 수정해야 한다.
2. 컴파일러는 원본과 새 클래스 간의 관계를 모른다. 따라서 새 클래스가 원본 클래스를 변형한 것으로 취급하지 않기 때문에 다형성(Polymorphic)을 구현할 수 없다.
3. 이 방법은 진정한 is-a 관계가 아니다. 새 클래스의 코드 중 상당 부분이 원본과 같아서 서로 비슷할 수는 있지만 그렇다고 타입이 같은 것은 아니다.
4. 원본 클래스의 소스코드를 구할 수 없거나, 소스코드를 직접 복사할 수 없는 경우, 원본 클래스로부터 새 클래스를 만들 수 없다.

이를 해결하기 위해서는 상속(Inheritance)이 필요하다.



## 2. C++에서의 상속

```c++
#include <iostream>

class FireArm
{
    private:
        int FireArmInt;

    protected:
        int FireArmProtectedInt;

    public:
        int FireArmPublicInt;

        FireArm() {
            FireArmInt = 10;
            FireArmProtectedInt = 20;
            FireArmPublicInt = 30;
        }

        void FierArmFunc() {
            std::cout << "FireArmFunc Called.";
        }
};

class AssaultRifle : public FireArm
{
    public:
        AssaultRifle() {
            // 상속받은 관계라도 부모 클래스에 있는 private 멤버를 상속받을 순 없다. 접근 가능하고 상속 받을 수 있는 대상은 protected와 public 뿐이다.
            // FireArmInt = 40
            FireArmProtectedInt = 50;
            FireArmPublicInt = 60;
        }
};

class Shotgun : protected FireArm
{
    public:
        Shotgun() {
            FireArmProtectedInt = 70;
            FireArmPublicInt = 80;
        }
};

class Pistol : private FireArm
{
    public:
        Pistol() {
            FireArmProtectedInt = 90;
            FireArmPublicInt = 100;
        }
};

int main( void )
{
    FireArm *musket = new FireArm();
    AssaultRifle *k2 = new AssaultRifle();
    Shotgun *m1897 = new Shotgun();
    Pistol *glock = new Pistol();

    std::cout << musket->FireArmPublicInt << std::endl;
    std::cout << k2->FireArmPublicInt << std::endl;

    // 접근 한정자에서 Shotgun은 protected로 받았다. 이는 부모 클래스의 물려받을 수 있는 멤버들을 protected 접근 지정자로 받겠다는 것을 의미한다.
    // std::cout << m1897->FireArmPublicInt << std::endl;
    // 접근 한정자에서 Pistol은 private로 받았다. 이는 부모 클래스의 물려받을 수 있는 멤버들을 private 접근 지정자로 받겠다는 것을 의미한다.
    // std::cout << glock->FireArmPublicInt << std::endl;
}
```

```
30
60
```

코드의 출력 결과를 살펴보면 30과 60이 출력된 것을 확인할 수 있다. 이 때, 30의 경우에는 당연한 결과이다. FireArm 클래스의 생성자가 FireArmPublicInt라는 변수를 30으로 초기화시키기 때문이다. 하지만 60의 경우는, AssaultRifle 클래스의 생성자가 FireArmPublicInt를 60으로 초기화하는데, 이 변수는 해당 클래스에서 선언된적이 없다. 즉, 상속으로 받아온 변수가 자신의 멤버 변수가 되는 것이다.

상속을 받을때, 접근제어 지시자를 통해서 상속받는 멤버들의 접근제어 지시자를 지정해줄 수 있다. 65번 라인과 67번 라인은 주석처리되어 있는데, 이는 해당 클래스가 부모 클래스인 FireArm으로부터 상속을 받을 때 어떤 접근제어 지시자를 사용했는지를 확인하면 왜 주석 처리된 코드들이 동작하지 않는지 알 수 있다. Shotgun 클래스의 경우에는 접근제어 지시자로 protected를 사용했다. 이는 상속받는 멤버들을 전부 protected로 접근제어 지시자를 사용한 것과 동일하다. 마찬가지로, Pistol 클래스의 상속 받을 때의 접근제어 지시자로 private를 사용했다. 이렇게 하면 상속받은 모든 멤버들이 private 접근제어 지시자로 접근이 제한된 것과 동일하다.

만약 특정 클래스가 더 이상 상속되지 못하게 하려면 어떻게 해야 할까. C++에서는 특정 클래스가 더 이상 상속될 수 없음을 나타내는 키워드로 final을 사용한다[^1]. final의 사용예는 아래와 같다.

```c++
class FinalClass final
{
    …
}
```





[^1]: 별준, "[C++] 클래스(Class) 상속 (1) - 확장, 재사용", 별준 코딩, https://junstar92.tistory.com/314 2022. 02. 14, Accessed on : 2022-08-30 08:08:09
[^2]: 김용성, "김용성의 C/C++ 완벽가이드", 영진닷컴, 2007. 04. 05.
[^3]: "클래스(Class)와 구조체(Struct)의 차이", 오늘의 공부, 2020. 01. 15. 02:54, Accessed on : 2022-08-30 09:33:30
[^4]: 단미라이프, "[Java] 상속(Inheritance) - (1)상속 개념, 상속 특징", 단미라이프, 2022. 03. 24 17:38, Accessed on : 2022-08-30 11:33:10

