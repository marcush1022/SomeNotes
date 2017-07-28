05-Cocos2dx3.0: C++11新特性
====

* **1.nullptr：用来代替NULL，nullptr是强类型，防止出现一些二义性**
----

```
void f(int); //#1  
void f(char *);//#2  
//C++03  
f(0); //二义性  
//C++11  
f(nullptr) //无二义性，调用f(char*) 
//因为C++中不能将void *类型的指针隐式转换成其他指针类型，而又为了解决空指针的问题，所以C++中引入0来表示空指针
```
 
* **2.auto：根据上下文自动类型推导**
----

``` 
bool AppDelegate::applicationDidFinishLaunching() {  
  // initialize director  
  auto director = Director::getInstance();              // Director*  
  auto glview = director->getOpenGLView();              // GLView*  
  ...  
}  
```
* **3.override：派生类重写基类的虚函数时，在函数的声明中加上override(非必须), 这样可在编译时检测出对基类函数的错误重写**

```
struct B {  
    virtual void f();  
    virtual void g() const;  
    virtual void h(char);  
    void k();              
};  
struct D : B {  
    void f() override;  //正确，覆盖B::f()
    void g() override;  //错误，派生类也需要加const  
    virtual void h(char);   //覆盖B::h()，会有警告  
    void k() override;  //错误，B::k()非虚  
};  
```

* **4.final:可用来修饰基类的虚函数，表示该函数不可被派生类重写即override**
----

```
struct B {  
    virtual void f() const final;   
    virtual void g();  
};  
  
struct D : B {  
    void f() const;     //报错，试图覆盖带有const的B::f()  
    void g();       //正确
};  
```

* **5.lambad表达式：主要应用时标书某些具有简单行为的函数**
----

例子：
```
flaglistener->onTouchEnded = [&](Touch* touch, Event* event){
		static_cast<TouchLayer*>(this->getParent())->tower = this->getTower();
		static_cast<TouchLayer*>(this->getParent())->setRallyFlagTouchShield();
		tower->isUpdateMenuShown = false;
		this->removeFromParent();
	};
```

