写复杂类的测试的时候常常碰到这类问题：想要改变某个私有的状态变量，碰巧这个变量又没有 set 方法，通过业务代码来修改又特别复杂，这个时候一个常见的做法就是侵入式的修改代码，譬如说通过宏去控制某些特定的 set 方法的编译，或者增加一个 friend class 声明，在测试用例里去实现该 friend class，又譬如直接 #define private public 一把梭哈（划掉）。
有没有什么非侵入式的方法可以访问，c++标准中的显式实例化给了一条路。标准原文[1]如下：
The usual access checking rules do not apply to names in a declaration of an explicit instantiation or explicit specialization, with the exception of names appearing in a function body, default argument, base-clause, member-specification, enumerator-list, or static data member or variable template initializer.
Note 1: In particular, the template arguments and names used in the function declarator (including parameter types, return types and exception specifications) can be private types or objects that would normally not be accessible.
也就是说，对于显式实例化或者显式全/偏特化，编译器是不会对模板参数做访问权限检查的。那么就可以把私有变量的成员指针作为模板参数传入模板之中。

```cpp
template <class MemberType, MemberType MemberPtr> struct ExposerImpl {};
template struct ExposerImpl<decltype(&Class::Attr), &Class::Attr>;
```
其中，&Class::Attr 可以获得类成员的成员指针，可以理解为 Attr 在类内存布局中的相对位置，我们可以通过对象和成员指针来访问储存在对象中的对应成员。
但是在使用中，我们不能直接使用 ExposerImpl<decltype(&Class::Attr), &Class::Attr>，因为在使用过程中，c++标准不允许使用类的私有成员作为模板参数，我们需要一个仅包含MemberType的类用于访问，并使用静态成员来记录成员指针，扩展代码如下：

```
template <class MemberType> struct Exposer {
  static MemberType memberPtr;
};
template <class MemberType, MemberType MemberPtr> struct ExposerImpl {
  static struct ExposerFactory {
    ExposerFactory() { Exposer<MemberType>::memberPtr = MemberPtr; }
  } factory;
};
```
通过ExposerImpl中静态成员的构造函数来对Exposer中静态变量进行赋值，达到储存成员指针的效果。
使用时对成员指针解引用即可

```
T.*exposer::Exposer<AttrType Class::*>::memberPtr;
```
完整的模板代码如下：

```
template <class MemberType> struct Exposer {
  static MemberType memberPtr;
};
template <class MemberType> MemberType Exposer<MemberType>::memberPtr;

template <class MemberType, MemberType MemberPtr> struct ExposerImpl {
  static struct ExposerFactory {
    ExposerFactory() { Exposer<MemberType>::memberPtr = MemberPtr; }
  } factory;
};
template <class MemberType, MemberType Ptr>
typename ExposerImpl<MemberType, Ptr>::ExposerFactory
    ExposerImpl<MemberType, Ptr>::factory;
```
Github中我还增加了一个宏可以快速的暴露私有变量，示例代码：

```
#include "exposer.hpp"

class Test {
  double a = 0.5;
};

ACCESS(Test, a, double)
int main {
  Test t{};
  ::get_a_from_Test(t) -= 1;
}
```
链接：[https://github.com/HerrCai0907/cpp-private-exposer/blob/main/src/exposer.hpp](https://link.zhihu.com/?target=https%3A//github.com/HerrCai0907/cpp-private-exposer/blob/main/src/exposer.hpp)

[1] [https://eel.is/c++draft/temp.sp](https://link.zhihu.com/?target=https%3A//eel.is/c%2B%2Bdraft/temp.spec%23general-6)
