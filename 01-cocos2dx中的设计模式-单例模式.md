01-cocos2dx中的设计模式-单例模式
=====

**单例模式的定义：保证一个类仅有一个实例，并提供一个访问它的全局的访问点**<br>

1. Cocos2D-x中的单例包括：
CCDirector,CCTextureCache,CCSpriteFrameCache,CCAnimationCache,CCUserDefault, <br>
CCNotificationCenter，CCShaderCache，CCScriptEngineManager，CCPoolManager，CCFileUtils, <br>
CCProfiler，SimleAudioEngie，CCConfiguration，CCApplication，CCDirectorCaller（ios平台），CCEGLView. <br>
<br>
2. 存在单例原因
<br>
* CCDirector单例：<br>
它负责管理初始化OpenGL渲染窗口以及游戏场景的流程控制，是cocos2dx游戏开发中必不可少的类之一，<br>
为什么要把此类设计成单例对象呢？因为，一个游戏只需要有一个游戏窗口就够了，所以，只需要初始化一次OpenGL渲染窗口，<br>
而且场景的流程控制功能，也只需要存在一个这样的场景控制对象即可。为了保证CCDirector类只存在一个实例对象，就必须<br>
使用单例模式.<br>
<br>
* CCUserDefault单例：<br>
此类主要是用来保存游戏中的数据，会创建一个xml文件，并把用户自定义的数据以key-value的形式存储到此xml文件中，<br>
此类是单例类的原因很简单，因为类似这种操作数据文件，或者配置文件的类，通常只需要在程序运行过程中存在一个实 <br>
例即可.<br>
<br>
* CCTextureCache单例：<br>
此类主要负责加载游戏当中所需要的纹理图片资源，这些资源加载好以后，就可以一直保留在内存里面，当下次再需要使用<br>
此纹理的时候，直接返回相应的纹理对象引用就可以了，无需再重复加载.<br>
<br>
* CCConfiguration: <br>
此类主要负责管理cocos2d-x的一些OpenGL变量信息。这些信息本可以通过定义一些宏，或者通过一些全局变量来解决的。<br>
这里设计成单例类也是更加“面向对象”的体现。因为这些配置信息根本不需存在多个对象. <br>
<br>
* CCApplication: <br>
此类的设计初衷是获得平台相关的一些信息，最重要的是运行游戏的主循环（main loop）。一个游戏只需要一个应用程序实例.<br>
<br>
* CCNotificationCenter: <br>
这是一个通知中心，它其实还运用了观察者模式。该通知中心理论上也是只需要一个就够了。但是，cocos2d-x在实现此单例的 <br>
时候，并没有将此类的构造函数私有.(为什么?) <br>
<br>
* CCPoolManager: <br>
此类是用来管理AutoReleasePool对象栈的。因为cocos2d-x采用的是基于引用计数的方式来管理动态内存，所以采用引用计数<br>
的被托管对象都被放入一个当前的autoReleasePool里面去了。当CCDirector的mainLoop每次更新的时候，都会调用<br>
CCPoolManager的pop方法，把当前autoReleasePool里面的所有autoRelease对象的托管状态设置为false，同时把该<br>
autoReleasePool清空，而清空的时候则会调用autoReleasePool里面所有对象的release方法来释放内存。此类为什么要设计成<br>
单例呢？因为多个地方需要引用此类，为了方便引用，所以设计成单例.<br>
<br>
* CCFileUtils: <br>
该类是一个工具类。工具类和配置文件类，它们绝大多数情况也都是设计成单例的。因为它们没有存在多个实例的必要。同时，<br>
它们也可以实现为一组类方法，这样无需创建对象也能够使用. <br>
<br>
* CCProfiler: <br>
该类负责cocos2d的性能其运行情况分析，也是一个工具类。所以它设计成单例类的理由与CCFileUtils类差不多.<br>
<br>
3. 使用单例模式的优缺点
* 优点： <br>
简单易用，限制一个类只有一个实例，可以降低创建多个对象可能会引起的内存问题的风险，包括内存泄漏、内存占用问题.<br>
<br>
* 缺点：<br>
单例模式因为提供了一个全局的访问点，你可以在程序的任何地方轻而易取地访问到，这本身就是一种高耦合的设计。一旦单<br>
例改变以后，其它模块都需要修改。另外，单例模式使得对象变成了全局的了(全局变量破坏了函数的封装性能, 不是万不得已，<br>
最好不要使用全局变量), 而且单例模式会使得对象的内存在程序结束之前一直存在，在一些使用GC的语言里面，这其实就是一种<br>
内存泄漏，因为它们永远都不到释放<br>
<br>
4. 游戏开发中如何运用单例模式
* 游戏数据保存和加载。这些数据包括关卡数据、游戏进行中的状态数据等。这样一些信息很多游戏模块中都需要访问，所以可以为 <br>
之设置一个单例对象. <br>
* 单例模式一般与工厂模式配合使用，因为一般会将工厂类设计成单例对象。比如前面提到的各种cache类，它们也可以看作是某种意<br>
义上的工厂对象。由于工厂就是负责生产对象的，而cache类都可以根据用户的需要生产出相应的对象. <br>

