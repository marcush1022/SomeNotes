16-cocos2dx中的设计模式-观察者模式
====

观察者模式，也叫订阅/发布模式，是MVC(视图、模型、控制器)模式的重要组成部分

典型的应用场景：
* GameScene中有两个Layer；
* 一个Layer为BaseMap，包含了游戏中的一些对象，比如敌人、玩家状态等等；
* 一个Layer为PlayerStateMenu，包含了显示玩家金币数、当前生命等信息；
* 需要将BaseMap中的实时玩家信息传递到PlayerStateMenu中显示；
* 使用观察者模式，让PlayerStateMenu类订阅BaseMaple类的信息，就可以实现数据传递。

注：3.0中用EventListenerCustom取代了NotificationCenter.

NotificationCenter
----

观察者类NotificationCenter，类似Director，也是单例类，主要用来订阅与发布消息。

使用：通过NotificationCenter:getInstance()获取单例对象。

有3个核心函数与观察者数组：<br>
订阅消息：addObserver()	<br>
发布消息：postNotification() <br>
退定消息：removeNotification() <br>
观察者数组：_observers <br>

观察者的对象就是NotificationObserver类，他的作用就是将定阅的消息、相应的订阅者和订阅者绑定的回调函数联系起来.

NotificationCenter/Observer类的主要方法如下：

```
//NotificationObserver类在addObserver时会自动创建
class CC_DLL NotificationObserver : public Ref {
    private:
        Ref* _target;            // 观察者主体对象
        SEL_CallFuncO _selector; // 消息回调函数
        std::string _name;       // 消息名称
        Ref* _sender;            // 消息传递的数据
 
    public:
        // 创建一个观察者对象
        NotificationObserver(Ref *target, SEL_CallFuncO selector, const std::string& name, Ref *sender);
 
        // 当post发布消息时，执行_selector回调函数，传入sender消息数据
        void performSelector(Ref *sender);
};
 

//消息订阅/发布中心类
class CC_DLL __NotificationCenter : public Ref {
    private:
        // 保存观察者数组 NotificationObserver
        __Array *_observers;
 
    public:
        // 获取单例对象
        static __NotificationCenter* getInstance();
        static void destroyInstance();
 
 
        // 订阅消息。为某指定的target主体，订阅消息。
        // target   : 要订阅消息的主体（一般为 this）
        // selector : 消息回调函数（发布消息时，会调用该函数）
        // name     : 消息名称（类型）
        // sender   : 需要传递的数据。若不传数据，则置为 nullptr
        void addObserver(Ref* target, SEL_CallFuncO selector, const std::string& name, Ref* sender);
 
 
        // 发布消息。根据某个消息名称name，发布消息。
        // name   : 消息名称
        // sender : 需要传递的数据。默认为 nullptr
        void postNotification(const std::string& name, Ref* sender = nullptr);
 
 
        // 退订消息。移除某指定的target主体中，消息名称为name的订阅。
        // target   : 主体对象
        // name     : 消息名称
        void removeObserver(Ref* target,const std::string& name);
		
        // 退订消息。移除某指定的target主体中，所有的消息订阅。
        // target   : 主体对象
        int removeAllObservers(Ref* target);
};
```

**工作原理：通过addObverser订阅消息，NoificationCenter会自动创建一个对象NotificationObserver，即观察者，然后将observer添加到观察者数组
_observers中。**

**发布消息时，遍历_observers数组，查找消息名为name的所有订阅，然后执行其观察者target绑定的回调函数selector。**
