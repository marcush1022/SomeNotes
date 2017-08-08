18-cocos2dx-贝塞尔曲线
====

致谢：http://www.cppblog.com/wc250en007/archive/2013/11/08/188048.html

1.贝塞尔曲线概念
----

贝塞尔曲线是应用于二维图形应用程序的数学曲线。曲线的定义有四个点：起始点、终止点（也称锚点）以及两个相互分离的中间点。
滑动两个中间点，贝塞尔曲线的形状会发生变化。

<img src="https://github.com/marcush1022/SomeNotes/blob/master/img/Bezier.jpg" width="50%" height="50%" />

其中p0, p3为起点终点, p1p2为控制点。

2.应用：模拟射弹轨迹
----

在cocos2dx中可使用bezier模拟射弹轨迹。
cocos2dx提供了2个action CCBezierBy和CCBezierTo，使用比较简单，只需要填充结构体。

例：

```
    //
    ccBezierConfig bezier;
		if(shootTag == 1)//播放射箭音效  
			SoundManager::playArrowShoot1();
		else
			SoundManager::playArrowShoot2();
		//计算弧线两个控制点, 即起始位置的上方与终点位置的上方，高度设置为200
    //Point shootVector = nearestMonster->baseSprite->getPosition() - this->getParent()->getPosition();
		bezier.controlPoint_1 = Point(currBullet->getPosition().x,currBullet->getPosition().y+200); 
		bezier.controlPoint_2 = Point(shootVector.x,shootVector.y+200);; 
		bezier.endPosition = shootVector;

		//Spwan将BezierTo与弓箭旋转动画合成为一个action，将action传递给子弹类，由子弹类具体执行
		auto action = Spawn::create(BezierTo::create(0.5f, bezier),RotateTo::create(0.5f,endAngle),NULL);
		//攻击属性给弓箭, bulletAction为子弹飞行动画
		currBullet->setBulletAction(action);
		//执行子弹类的shoot，攻击动画完成
		currBullet->shoot();
		currBullet = NULL;
```

