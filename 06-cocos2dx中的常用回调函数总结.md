06-cocos2dx中的常用回调函数总结
====

* **1. schedule家族**
----

scheduleUpdate: <br>
通过this->scheduleUpdate() 把定时器加到节点后，节点会每帧都会调用虚函数：update(void)<br>
取消方法：this->unscheduleUpdate(); 只能触发虚函数 update()

schedule: <br>
定义是 void CCNode::schedule(SEL_SCHEDULE selector, float interval, unsigned int repeat, float delay)<br> 
通过this->schedule(SEL_SCHEDULE selector, float interval, unsigned int repeat, float delay) 把定时器加到节点，可以指定回调函数、触发间隔、
重复次数、延迟启动时间，第二个参数（触发间隔）为0则表示每帧都触发，相当于scheduleUpdate，但优势在于可以自己指定回调方法；<br>
取消方法：this->unschedule(SEL_SCHEDULE selector);

scheduleOnce: <br> 
定义是 void CCNode::scheduleOnce(SEL_SCHEDULE selector, float delay);<br>
this->scheduleOnce(selector, delay) 把定时器加到节点，指定回调函数和延迟启动时间，只会触发一次。<br> 
取消方法：this->unscheduleOnce(selector); <br>


* **2. callFunc 家族**
----

当需要在一个动作序列中某一个动作执行结束之后，调用某个函数用于执行一个任务的时候，可以使用CCCallFunC家族函数
CCCallFunC家族函数可以将函数调用的过程封装成一个动作类，从而放入动作序列中以供我们调用。 

CallFunc可以直接使用CC_CALLBACK_0、CC_CALLBACK_1，也可以直接使用std::bind。<br>
CallFunc: 使用CC_CALLBACK_0。不带任何不定参数。<br>
CallFuncN：使用CC_CALLBACK_1。<br>

例子：

```
CallFunc::create ( CC_CALLBACK_0(HelloWorld::callback0, this) );
CallFuncN::create( CC_CALLBACK_1(HelloWorld::callback1, this) );
CallFuncN::create( CC_CALLBACK_1(HelloWorld::callback2, this, 0.5));
```

**菜单项回调:**

```
CC_CALLBACK_1
MenuItemImage::create("1.png", "2.png", CC_CALLBACK_1(HelloWorld::callback1, this));
```

**触控事件回调:**

使用CC_CALLBACK_2, 例如：

```
//创建一个事件监听器类型为单点触摸
 auto touchLisner = EventListenerTouchOneByOne::create();
 
//绑定事件
touchLisner->onTouchBegan = CC_CALLBACK_2(HelloWorld::onTouchBegan, this);
touchLisner->onTouchMoved = CC_CALLBACK_2(HelloWorld::onTouchMoved, this);
touchLisner->onTouchEnded = CC_CALLBACK_2(HelloWorld::onTouchEnded, this);
 
//回调函数
virtual bool HelloWorld::onTouchBegan(Touch *touch, Event *unused_event); 
virtual void HelloWorld::onTouchMoved(Touch *touch, Event *unused_event); 
virtual void HelloWorld::onTouchEnded(Touch *touch, Event *unused_event);
```

