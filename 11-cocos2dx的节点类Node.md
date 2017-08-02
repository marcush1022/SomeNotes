11-cocos2dx的节点类
====

Node是大多数类的父类，但不是所有，比如Direct类是直接继承于Ref. 如scene，layer, sprite以及精灵集
合spriteBatchNode等的父类都是Node，Node类包含了一些基本属性，节点相关，action动作执行以及定时器
相关操作.

Node的父类为Ref.

一个节点的主要特点：
* 可以包含其他的节点对象（addchild, getChildTag, removeChild, etc）
* 可以安排定期的回调(schedule, unschedule, etc)
* 可以执行一些动作(runAction, stopAction, etc)
* 节点的属性有：位置，缩放，旋转，倾斜，锚点，内容大小，可见性

**1. 节点的属性**

```
//位置Postion, 设置节点的坐标
    virtual void setPosition(const Vec2 &position); //Vec2坐标
    virtual void setPosition(float x, float y); //(x,y),比Vec2更有效率
    virtual void setPositionX(float x);
    virtual void setPositionY(float y);
    virtual const Vec2& getPosition() const;
    virtual void  getPosition(float* x, float* y) const;
    virtual float getPositionX(void) const;
    virtual float getPositionY(void) const;
     
//增加3D坐标
    virtual void setPosition3D(const Vec3& position); //Vec3坐标
    virtual Vec3 getPosition3D() const;
    virtual void setPositionZ(float positionZ);
    virtual float getPositionZ() const;
     
//增加标准化坐标设置
    //Node的位置像素会根据它的父节点的尺寸大小计算
    //Size s = getParent()->getContentSize();
    //_position = pos * s;
    virtual void setNormalizedPosition(const Vec2 &position);
    virtual const Vec2& getNormalizedPosition() const;
 
 
//放缩，设置节点的缩放比例，对XYZ轴进行放缩
    virtual void setScaleX(float scaleX);              //放缩宽X
    virtual void setScaleY(float scaleY);              //放缩高Y
    virtual void setScaleZ(float scaleZ);              //放缩深Z
    virtual void setScale(float scaleX, float scaleY); //X放缩fScaleX倍,Y放缩fScaleY倍
    virtual void setScale(float scale);                //XYZ同时放缩scale倍
    virtual float getScaleX() const;
    virtual float getScaleY() const;
    virtual float getScaleZ() const;
    virtual float getScale()  const;                   //当x,y放缩因子相同时，得到该节点的缩放因子
 
 
//设置旋转
    virtual void setRotation(float rotation);
    virtual float getRotation() const;
 
//增加3D旋转
    virtual void setRotation3D(const Vec3& rotation); 
    virtual Vec3 getRotation3D() const;
 
 
//倾斜，设置XY轴的倾斜角度
    virtual void setSkewX(float skewX); //水平旋转倾斜.负顺时针变形
    virtual void setSkewY(float skewY); //垂直旋转倾斜
    virtual void setRotationSkewX(float rotationX);
    virtual void setRotationSkewY(float rotationY);
    virtual float getSkewX() const;
    virtual float getSkewY() const;
    virtual float getRotationSkewX() const;
    virtual float getRotationSkewY() const;
 
 
//设置锚点，锚点可能位于图片的左下角，中心或右下角
//默认的锚点为(0,.5, 0.5)，即节点的中心
    virtual void setAnchorPoint(const Vec2& anchorPoint); //标准化的锚点
    virtual const Vec2& getAnchorPoint() const;           //标准化的锚点
    virtual const Vec2& getAnchorPointInPoints() const;   //返回绝对像素的锚点,即屏幕坐标
 
//是否忽略锚点的设置, 若忽略锚点设置,锚点永远为(0,0)
    //默认值是false, 但是在Layer和Scene中是true
    //这是一个内部方法，仅仅被Layer和Scene使用，不要自行调用！
    virtual void ignoreAnchorPointForPosition(bool ignore);
    virtual bool isIgnoreAnchorPointForPosition() const;
 
 
//内容大小ContentSize，ContentSize依然是相同的，无论节点的缩放或旋转
//所有的节点，都有大小，图层与场景有相同的屏幕大小
    virtual void setContentSize(const Size& contentSize);
    virtual const Size& getContentSize() const;
 
//设置可见性
    virtual void setVisible(bool visible);
    virtual bool isVisible() const;
```

**2. 节点的操作**

节点的操作包括标记，名字，自定义数据，父节点管理，子节点管理等.

```
//设置节点的标记与名字
//setTag设置节点的编号，setName设置节点的名字
    virtual void setTag(int tag);
    virtual void setName(const std::string& name);
    virtual int getTag() const;
    virtual std::string getName() const;
 
 
//自定义数据(UserData, UserObject)
    virtual void setUserData(void *userData);
    virtual void setUserObject(Ref *userObject);
    virtual void* getUserData();
    virtual Ref* getUserObject();
 
 
//设置父节点，
    virtual void setParent(Node* parent);
    virtual Node* getParent();
    virtual void removeFromParent();
	//true则删除该节点的所有动作及回调函数.
    virtual void removeFromParentAndCleanup(bool cleanup); 
 
 
//管理子节点
    //添加子节点
    //localZOrder   Z轴顺序为了sprite的优先权
    //tag, name      通过tag或name获取子节点
    virtual void addChild(Node * child);
    virtual void addChild(Node * child, int localZOrder);
    virtual void addChild(Node* child, int localZOrder, int tag);
    virtual void addChild(Node* child, int localZOrder, const std::string &name);
 
    //获取子节点
    virtual Node* getChildByTag(int tag) const;
    virtual Node* getChildByName(const std::string& name) const;
    virtual Vector<Node*>& getChildren();     //获得所有子节点,并以Vector数组返回
    virtual ssize_t getChildrenCount() const; //子节点总数
 
    //删除子节点
    virtual void removeChild(Node* child, bool cleanup = true);
    virtual void removeChildByTag(int tag, bool cleanup = true);
    virtual void removeChildByName(const std::string &name, bool cleanup = true);
    virtual void removeAllChildren();                        //删除所有节点
	//cleanup为true则删除子节点的所有动作
    virtual void removeAllChildrenWithCleanup(bool cleanup); 
 
    //重新排序一个子节点，设定一个新的z轴的值
    //child 必须是已经添加的
    virtual void reorderChild(Node * child, int localZOrder);
    virtual void sortAllChildren(); //重新排序所有子节点
 
 
//其他的操作
//节点开始进入舞台scene时调用.即创建时调用.
    virtual void onEnter();    
//节点进入舞台scene后调用.即创建完后调用.	
    virtual void onEnterTransitionDidFinish(); 
	//节点离开舞台时调用.即移除时调用
    virtual void onExit();       
//节点离开舞台前调用.	
    virtual void onExitTransitionDidStart();   
 
    //绘制节点
    virtual void draw() final;
    //递归访问所有子节点,并重新绘制  
    virtual void visit() final;
 
    //返回包含Node(节点)的Scene(场景). 
    //若不属于任何的场景,它将返回nullptr
    virtual Scene* getScene() const;
     
    //返回节点在父节点坐标中的矩形边界框
    virtual Rect getBoundingBox() const;
     
    //暂停所有的活动着的动作和调度器
    virtual void cleanup();
```

**3. 动作action相关**

动作包括运行，暂停和取消操作.

```
//action管理
//设置被所有动作使用的ActionManager对象，如果设置了一个新的ActionManager, 那么之前创建的动作将会被删除
    virtual void setActionManager(ActionManager* actionManager);
    virtual ActionManager* getActionManager();
 
    Action* runAction(Action* action);          //执行一个动作
    Action* getActionByTag(int tag);            //获取动作, 根据tag标记
    void stopAllActions();                      //暂停动作
    void stopAction(Action* action);            //暂停动作
    void stopActionByTag(int tag);              //暂停动作
    ssize_t getNumberOfRunningActions() const;  //获取正在运行的动作数量
```

**4. 定时器schedule相关**

包括定时器管理，默认定时器，一次性定时器，自定义定时器.

```
//设置一个调度器schedule对象，来调度所有的“update”和定时器,如果设置了一个新的调度器，
//那么之前创建的timers/update将会被删除。
    virtual void setScheduler(Scheduler* scheduler);
    virtual Scheduler* getScheduler(); //得到调度器对象
 
    //开启默认定时器.刷新次数为60次/秒.即每秒60帧.
    //与update(float delta)回调函数相对应.
    //给予定时器优先级priority.其中priority越小,优先级越高
    void scheduleUpdate(void);
    void scheduleUpdateWithPriority(int priority);
    void unscheduleUpdate(void);      //取消默认定时器
    virtual void update(float delta); //update为scheduleUpdate定时器的回调函数.
 
    //设置自定义定时器.默认为每秒60帧.
    //每隔interval秒,执行一次.repeat为重复次数. delay为延迟时间,即创建定时器delay后开始执行.
    void schedule(SEL_SCHEDULE selector, float interval, unsigned int repeat, float delay);
    void schedule(SEL_SCHEDULE selector, float interval);
    void schedule(SEL_SCHEDULE selector);                  //默认为每秒60帧
    void scheduleOnce(SEL_SCHEDULE selector, float delay); //只执行一次,delay秒后执行
 
    void unschedule(SEL_SCHEDULE selector);                //取消一个自定义定时器
    void unscheduleAllSelectors(void);                     //取消所有定时器
    void resume(void);                                     //恢复所有定时器和动作
    void pause(void);                                      //暂停所有定时器和动作
```

**5. 坐标转换**

坐标分为两种坐标.<br>
世界坐标：即绝对坐标，世界指的是游戏世界，<br>
本地坐标：即相对坐标，相对节点的坐标. <br>

坐标转换函数：

```
//1. 把世界坐标, 转换到当前节点的本地坐标系中
    //基于Anchor Point, 把基于当前节点的本地坐标系下的坐标, 转换到世界坐标系中
    Vec2 convertToNodeSpace(const Vec2& worldPoint) const;
    Vec2 convertToNodeSpaceAR(const Vec2& worldPoint) const;
 
 
    //2. 把基于当前节点的本地坐标系下的坐标, 转换到世界坐标系中
    //基于Anchor Point. 把世界坐标, 转换到当前节点的本地坐标系中
    Vec2 convertToWorldSpace(const Vec2& nodePoint) const;
    Vec2 convertToWorldSpaceAR(const Vec2& nodePoint) const;
     
     
    //把触点坐标, 转换到当前节点的本地坐标系中
    Vec2 convertTouchToNodeSpace(Touch * touch) const;
    Vec2 convertTouchToNodeSpaceAR(Touch * touch) const;
```
	
在屏幕触控中的应用：

```
判断摸个触摸点是否位于精灵内部区域
bool HelloWorld::onTouchBegan(Touch *touch, Event *unused_event)
    {
        //将触点坐标, 转换为相对节点的相对坐标
        Vec2 point = sprite->convertTouchToNodeSpace(touch);
 
        //构造sp的尺寸矩形
        Size size = sprite->getContentSize();
        Rect rect = Rect(0, 0, size.width, size.height);
 
        //判断触点是否触摸到sp内部
        if (rect.containsPoint(point)) {
            CCLog("YES");
            return true;
        }
 
        return false;
    }
```
