17-cocos2dx的定时器schedule
====

定时器的作用：每隔一段时间，执行相应的刷新体函数，来更新游戏画面，时间，进度，敌人的指令等等。

cocos2dx的定时器操作函数定义在CCNode中，因此大部分的引擎类都可以使用定时器，例如CCLayer, CCMenu, CCSprite等。

定时器的更新方式分为3类：<br>
1. 默认定时器: scheduleUpdate() <br>
2. 自定义定时器: schedule() <br>
3. 一次性定时器: scheduleOnce() <br>

1.scheduleUpdate()
----

默认定时器的刷新次数与屏幕刷新率有关，若屏幕的刷新率为60帧每秒，则scheduleUpdate每秒执行60次刷新。<br>
与scheduleUpdate相应的刷新体函数为virtual void upate()，即每一帧执行一次update()函数。

相关操作：

```
//设置默认定时器，默认刷新间隔为1帧
void Node::scheduleUpdate();
void Node::scheduleUpdateWithPriority(int priority);
//update为定时器刷新函数体
virtual void update(float delta);
```

2.schedule()
----

自定义定时器可以自定义指定的刷新函数体、刷新函数体的次数、刷新间隔、开始刷新的时间等。
函数体不一定为update，可以自己定义。

相关操作：

```
void schedule(SEL_SCHEDULE selector); //默认刷新间隔为一帧
//例：schedule( schedule_selector(HelloWorld::myUpdate), 1.0/60.0 );
void schedule(SEL_SCHEDULE selector, float interval); //自定义刷新间隔，单位为秒
//interval: 每隔interval秒执行一次
//repeat: 重复次数
//delay: 延迟时间，即创建定时器delay秒后开始执行刷新
void schedule(SEL_SCHEDULE selector, float interval, unsigned int repeat, float delay);
```

3.scheduleOnce()
----

一次性定时器：等待delay秒后只执行一次函数体，之后就不再刷新。<br>
相关操作：

```
void scheduleOnce(SEL_SCHEDULE selector, float delay);
//例：scheduleOnce( schedule_selector(HelloWorld::myUpdate), 5.0 ); 
```

4.其他操作
----

```
void unscheduleUpdate(void);            //取消默认定时器
void unschedule(SEL_SCHEDULE selector); //取消自定义函数的定时器
void unscheduleAllSelectors(void);      //取消所有定时器
void pauseSchedulerAndActions(void);    //暂停所有定时器和动作
void resumeSchedulerAndActions(void);   //恢复所有定时器和动作

//例：
//this->unscheduleUpdate();
//sprite->unscheduleAllSelectors();
```

5.举例
----

```
//Assassin士兵类状态更新方法
void Assassin::cheakState()
{
	if(nearestMonster!=NULL){
    //默认定时器
		scheduleUpdate();
    //自定义定时器，刷新函数为Assassin::attackMonster(float dt)
		schedule(schedule_selector(Assassin::attackMonster), 1.0f,-1,0);
	}else{
		runToLocation(getLocation());
	}
}
```

```
//默认定时器对应的update刷新函数
void Assassin::update(float dt)
{
	//若状态更新
	if(lastState!=getState()){
		//根据状态判断
		switch (getState())
		{
		case(SoldierStateRun):{
			lastState = SoldierStateRun;
			//停止之前动画
			stopSoldierAnimation();
			auto action = RepeatForever::create(Animate::create(AnimationCache::getInstance()->getAnimation("Assassin_run")));
			action->setTag(SoldierStateRun);
			baseSprite->runAction(action);}
			break;
		case(SoldierStateHit):{
			lastState = SoldierStateHit;
			stopSoldierAnimation();
			auto action = RepeatForever::create(Animate::create(AnimationCache::getInstance()->getAnimation("Assassin_attack")));
			action->setTag(SoldierStateHit);
			baseSprite->runAction(action);}
			break;
		case(SoldierStateWait):{
			lastState = SoldierStateWait;
			stopSoldierAnimation();
			baseSprite->setSpriteFrame(SpriteFrameCache::getInstance()->getSpriteFrameByName("Soldier_Assassin_0001.png"));}
			break;
		case(SoldierStateSkill1):{//致命一击
			lastState = SoldierStateSkill1;
			stopSoldierAnimation();
			auto action = RepeatForever::create(Animate::create(AnimationCache::getInstance()->getAnimation("Assassin_skill1")));
			action->setTag(SoldierStateSkill1);
			baseSprite->runAction(action);}
			break;
		case(SoldierStateSkill2):{//闪避
			lastState = SoldierStateSkill2;
			stopSoldierAnimation();
			auto action = RepeatForever::create(Animate::create(AnimationCache::getInstance()->getAnimation("Assassin_skill2")));
			action->setTag(SoldierStateSkill2);
			baseSprite->runAction(action);}
			break;
		}
	}
}
```

