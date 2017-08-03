14-cocos2dx中的设计模式-外观模式
====

**外观模式（facade）**: 一个复杂的系统包含了很多子系统，为了使用这个复杂的系统，我们定义一个统一
的接口来使用这个复杂的系统。当用户操作的时候只要调用我们提供的这个接口就好了，至于底层的这个复杂的系统，
用户不必关系是如何工作的. <br>

cocos2dx中用到很多，最普遍的例如那些create函数，只要用户一看就知道是干什么的，也不需要知道里面有什么
操作，如果让用户自己去写，就需要先构造一个对象，再将其加入自动内存管理系统中，比较麻烦，还可能出错. <br>

另一个非常典型的采用外观模式的地方就是SimpleAudioEngine. <br>
注意，SimpleAudioEngine并没有增加新的功能，而只是把子系统现有的类进行组合来完成一些常用的任务，简化客户程序的使用.

**SimpleAudioEngine**

SimpleAudioEngine中的方法：

```
    //预加载
    virtual void preloadBackgroundMusic(const char* filePath);    
    //播放，是否循环，默认不循环
    virtual void playBackgroundMusic(const char* filePath, bool loop = false); 
    //停止，是否释放音乐资源
    virtual void stopBackgroundMusic(bool bReleaseData = false); 
    //暂停
    virtual void pauseBackgroundMusic();    
    //继续
    virtual void resumeBackgroundMusic();   
    //重播
    virtual void rewindBackgroundMusic();                                        
         
 
    //返回是否将要播放背景音乐
    virtual bool willPlayBackgroundMusic();
 
    //返回是否正在播放背景音乐
    //注意：暂停也算正在播放，只有停止了才算未播放。
    virtual bool isBackgroundMusicPlaying(); 
 
    //设置音量，取值范围0~1.0
    //3.x中实现了音量的调节
    virtual float getBackgroundMusicVolume();
    void setBackgroundMusicVolume(float volume);
```

例：

```
//SoundManager.cpp
void SoundManager::playAssassinMove()
{
	if(cheakEffectState())
    //加载声音文件
		SimpleAudioEngine::getInstance()->playEffect("sound/assassin_taunt_ready.wav");
}
```

```
//BaseBarrackTower.cpp
void BaseBarracksTower::setRallyPoint(Point point)
{
	if(level == 1||level == 2||level ==3)
		SoundManager::playBarrackMove();
	else if(level == 4 )
    //播放声音
		SoundManager::playAssassinMove();
    ...
```

**使用外观模式的优缺点**

优点：<br>

1. 它对客户屏蔽子系统组件，因而减少了客户处理的对象的数目，并使得子系统使用起来更加方便.

2. 它实现了子系统与客户之间的松耦合关系，而子系统内部的功能组件往往是紧密耦合的，这样当子系统功能组件发生
变化的时候，只需要修改外观类的实现就可以了.

3. 同时，外观类并不限制客户直接使用子系统的功能组件，如果客户想使用子系统的更加高级的功能，可以越过外观类直接访问子系统的类.

缺点：<br>

过多的或者不太合理的Façade也容易让人迷惑。到底是调用Façade好呢，还是直接调用子系统的模块好呢.
    
