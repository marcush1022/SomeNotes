15-cocos2dx中的设计模式-简单工厂模式
====

从设计模式的类型上来说，简单工厂模式是属于创建型模式，又叫做静态工厂方法（Static Factory Method），
通过专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。

简单工厂模式、抽象工厂模式与工厂模式的区别
----

1. 简单工厂模式的工厂类一般是静态方法，通过接收的参数不同返回不同的对象实例
不修改代码的话无法修改

2. 工厂方法是针对每一个产品提供一个工厂类，通过不同的工厂实例来创建不同的产品实例

在同一等级结构中，支持增加任意产品

3. 抽象工厂是应对产品概念的，例如，每个汽车公司可能要同时生产轿车、货车和客车，
那么每一个工厂类都应该有轿车客车货车的方法

例：
----

```
//BaseMap.cpp
//addMonsters()是BaseMap类的一个静态工厂方法，通过传入的怪物类型，统一创建对象.
void BaseMap::addMonsters(float dt)
{ 
	//waveVector.size()为波数
	//waveVector.at()保存该wave怪物，size为怪物个数
	//waveVector.at().at()保存该0.5s内需要创建的怪物,.size为怪物个数
	if( time < waveVector.at(wave).size())
	{
		for(int i=0 ;i<waveVector.at(wave).at(time).size();i++)
		{
			auto monsterData = waveVector.at(wave).at(time).at(i);
			switch (monsterData->getType())
			{
			case(0):{
				//将路线赋给怪物，让怪物根据路线在地图上移动，即可实现怪物行走
				//调用BaseMonster子类的createMonster函数，将从Plist读取的敌人行走路线点传给BaseMonster类，
				//并且设置一些基本属性
        //创建对象
				auto thug = Thug::createMonster(path.at(monsterData->getRoad()).at(monsterData->getPath()));
				addChild(thug);
				//将敌人加入单例数组 
				GameManager::getInstance()->monsterVector.pushBack(thug);}
				break;
			case(1):{
				auto raider = Raider::createMonster(path.at(monsterData->getRoad()).at(monsterData->getPath()));
				addChild(raider);
				GameManager::getInstance()->monsterVector.pushBack(raider);}
				break;
			case(2):{
				auto wolf = Wolf::createMonster(path.at(monsterData->getRoad()).at(monsterData->getPath()));
				GameManager::getInstance()->monsterVector.pushBack(wolf);
				addChild(wolf);}
				break;
        ....
```

