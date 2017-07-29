07-cocos2dx核心类
====

核心类:<br>
* Director
* Scene
* Layer
* Sprite

**1. 导演类Director**

导演类负责不同场景的切换，控制游戏流程(开始、继续、暂停等)，获取系统信息(如屏幕大小等).<br>
导演类与Scene, Layer, Sprite不同，其直接继承于Ref类而不是Node类.<br>

其中的部分方法：<br>
```
class CC_DLL Director : public Ref
{
    //获取单例对象
    //获取全局唯一的Director实例 , 替代 sharedDirector
    //使用方法：Director::getInstance()->replaceScene(scene);
    static Director* getInstance();
    
    /*****************场景管理方法*****************/
    //指定进入Director的主循环运行的场景，仅在运行第一个场景时调用,
    //如果已经存在运行中的场景,不能调用本方法
    //本方法调用后将调用pushScene方法,然后调用startAnimation.
    void runWithScene(Scene *scene);
 
    //暂停运行中的场景,并push到场景堆栈中,运行新的场景.
    void pushScene(Scene *scene);
 
    //从场景堆栈中pop出一个场景,替换正运行的场景,运行中的场景将被删除.
    void popScene();
 
    //从场景堆栈中pop出所有场景,最后一个栈底的场景将替换现在正运行的场景,运行中的场景将被删除.
    void popToRootScene();
 
    //弹出从队列中的所有场景，直到它到达 level.
    //如果 level 是 0,它将结束 director. 
    //如果 level 是 1, 从队列中弹出所有的场景，直到只有根场景在队列中. 
    //如果 level <= 当前的堆栈水平，不做任何事情。
    void popToSceneStackLevel(int level);
 
    //使用新场景替换当前场景,运行中的场景将被删除. 旧场景不压入堆栈,而是直接删除.
    void replaceScene(Scene *scene);
 
    void pause(void);                //暂停场景
    void resume(void);               //恢复被暂停的场景
    void end(void);                  //终止执行,释放运行中的场景
     
    inline Scene* getRunningScene(); //获取当前运行的场景
    inline bool isPaused();          //Director是否被暂停
 
 
    /*****************帧数FPS*****************/
    //设置程序的FPS值.即刷新频率,相连两帧的时间间隔.
    //例如dValue = 1.0/60.0 表示每秒60帧.
    virtual void setAnimationInterval(double interval) = 0;
    inline double getAnimationInterval();
 
    //是否在左下角显示FPS
    inline void setDisplayStats(bool displayStats);
    inline bool isDisplayStats();
     
    //获取每秒执行帧数
    inline float getSecondsPerFrame()
     
    //从Director开机后,总共已经渲染了多少帧
    inline unsigned int getTotalFrames();
```
  
**2. 场景Scene**
  
Scene继承于Node类，但是它只有create函数，因为场景就是一个容器，将不同的Layer组合在一起，便于管理.<br>
一个游戏会有很多场景，例如主界面，游戏界面载入界面等等。每个场景都是由多个图层组合在一起从而形成完整.<br>
的游戏画面.<br>
  
主要函数如下：<br>
```
class CC_DLL Scene : public Node
{
    static Scene *create();                         //场景大小和屏幕一样
    static Scene *createWithSize(const Size& size); //自定义大小
    virtual Scene *getScene() const override;       //覆盖场景
};
```

**3. 布景层Layer**

Layer继承于Node，继承了Node的所有操作，还附加了触控、重力加速度计、支持键盘输入事件的事件代理.<br>
一个Layer同样包含了多个元素，如标签（Label）、菜单（Menu）、精灵（Sprite）等.<br>
Layer的默认锚点是(0,0)，即左下角，并且忽略锚点的设置，锚点位置不会改变.<br>

其中的主要函数如下：<br>
```
class CC_DLL Layer : public Node
{
    static Layer *create();
 
    /******************触控相关方法**********************/
    //单点触控
    virtual bool onTouchBegan(Touch *touch, Event *unused_event); 
    virtual void onTouchMoved(Touch *touch, Event *unused_event); 
    virtual void onTouchEnded(Touch *touch, Event *unused_event); 
    virtual void onTouchCancelled(Touch *touch, Event *unused_event);
 
    //多点触控
    virtual void onTouchesBegan(const std::vector<Touch*>& touches, Event *unused_event);
    virtual void onTouchesMoved(const std::vector<Touch*>& touches, Event *unused_event);
    virtual void onTouchesEnded(const std::vector<Touch*>& touches, Event *unused_event);
    virtual void onTouchesCancelled(const std::vector<Touch*>&touches, Event *unused_event);
 
    /******************重力加速度计相关方法**********************/
    //加速度计监听事件回调函数
    virtual void onAcceleration(Acceleration* acc, Event* unused_event);
     
     
    /******************键盘监控相关方法**********************/
    //按下键盘, 监听事件回调函数
    virtual void onKeyPressed(EventKeyboard::KeyCode keyCode, Event* event);
    //释放键盘, 监听事件回调函数
    virtual void onKeyReleased(EventKeyboard::KeyCode keyCode, Event* event);
};
```

**4. 精灵类Sprite**

精灵实际上就是一个2D图片，通过赋予精灵各种状态如大小、颜色、旋转、缩放等等实现各种效果.<br>
精灵是在布景层Layer上的，一个Layer包含多个精灵.<br>
Sprite不仅继承了Node类，也继承了纹理协议接口TextureProtocol.<br>
TextureProtocol主要是用于纹理图片的管理.<br>
Sprite的默认锚点为(0.5,0.5)，即中心点.<br>

主要函数如下：<br>
```
//Sprite定义为二维图像, 可以通过一个图像或一个图像的矩形裁剪部分创建Sprite.
class CC_DLL Sprite : public Node, public TextureProtocol
{
    /***********************创建方法************************/
    static Sprite* create();                                                                    //默认创建空精灵对象
    static Sprite* create(const std::string& filename);                                         //通过图片文件(png)创建
    static Sprite* create(const std::string& filename, const Rect& rect);                       //截取图片文件中某区域图片创建
    static Sprite* createWithTexture(Texture2D *texture);                                       //纹理图片
    static Sprite* createWithTexture(Texture2D *texture, const Rect& rect, bool rotated=false); //截取纹理图片中某一区域图片,是否旋转
    static Sprite* createWithSpriteFrame(SpriteFrame *spriteFrame);                             //精灵帧,精灵帧一般从plist中读取
    static Sprite* createWithSpriteFrameName(const std::string& spriteFrameName);               //通过精灵帧的名字创建
 
 
 
    //批处理节点
    virtual void updateTransform(void);                          //更新四个值：position(x,y), rotation, scale
    virtual void setBatchNode(SpriteBatchNode *spriteBatchNode); //设置批节点,不推荐手工调用
    virtual SpriteBatchNode* getBatchNode(void);                 //如果精灵是由批节点渲染，则返回批节点
 
 
    /***********************纹理Texture************************/
    //设置精灵的纹理图片.
    virtual void setTexture(const std::string &filename ); //调用setTextureRect设置Sprite尺寸
    virtual void setTexture(Texture2D *texture) override;  //纹理的矩形尺寸大小不会改变
    virtual Texture2D* getTexture() const override;
 
    //设置Sprite纹理(texture)的Rect尺寸(rect)、是否旋转(rotated)、裁剪尺寸(untrimmedSize)。 
    //调用此方法会修改纹理(texture)的坐标和顶点位置
    virtual void setTextureRect(const Rect& rect, bool rotated = false, const Size& untrimmedSize = rect.size); 
 
 
    /***********************精灵帧SpriteFrames和动画Animation************************/
    //设置新的显示精灵帧. 替代setDisplayFrame
    virtual void setSpriteFrame(const std::string &spriteFrameName);
    virtual void setSpriteFrame(SpriteFrame* newFrame);
     
    //返回精灵帧是否正在显示
    virtual bool isFrameDisplayed(SpriteFrame *pFrame) const;
 
    //返回当前显示的精灵帧. 替代 getDisplayFrame
    virtual SpriteFrame* getSpriteFrame() const;
 
    //通过动画帧的第frameIndex那一帧来设置显示精灵帧
    //动画帧是从CCAnimationCache中读取的
    virtual void setDisplayFrameWithAnimationName(const std::string& animationName, ssize_t frameIndex);
 
 
    /***********************精灵属性相关************************/
    //设置Sprite在纹理集Atlas中是否需要更新
    virtual void setDirty(bool dirty);
    virtual bool isDirty(void) const;
     
    //返回四个值的信息：坐标(x,y),顶点,颜色
    inline V3F_C4B_T2F_Quad getQuad(void) const;
     
    //判断纹理是否被旋转
    inline bool isTextureRectRotated(void) const;
 
    //设置纹理集(TextureAtlas)的当前使用索引
    //警告: 除非你了解调用此方法的影响，否则不要改变此值
    inline void setAtlasIndex(ssize_t atlasIndex);
    inline ssize_t getAtlasIndex(void) const;
 
    //返回Sprite的Rect区域信息,单位点
    inline const Rect& getTextureRect(void);
 
    //如果采用批渲染,设置纹理地图集
    inline void setTextureAtlas(TextureAtlas *pobTextureAtlas);
    inline TextureAtlas* getTextureAtlas(void);
     
    //获取偏移值
    inline const Vec2& getOffsetPosition(void) const;
 
    //设置是否翻转。
    void setFlippedX(bool flippedX); //设置Sprite是否水平翻转。替代 setFlipX
    bool isFlippedX(void) const;
    void setFlippedY(bool flippedY); //设置Sprite是否垂直翻转。替代 setFlipY
    bool isFlippedY(void) const;
 
 
    /***********************继承于TextureProtocol************************/
    //设置颜色混合模式
    inline void setBlendFunc(const BlendFunc &blendFunc) override;
    inline const BlendFunc& getBlendFunc() const override;
 
 
    /***********************继承于Node************************/
    virtual void setScaleX(float scaleX) override;
    virtual void setScaleY(float scaleY) override;
    virtual void setScale(float scaleX, float scaleY) override;
    virtual void setScale(float scale) override;
 
    virtual void setPosition(const Vec2& pos) override;
    virtual void setPosition(float x, float y) override;
    virtual void setRotation(float rotation) override;
    virtual void setPositionZ(float positionZ) override;
 
    virtual void setSkewX(float sx) override;
    virtual void setSkewY(float sy) override;
    virtual void setRotationSkewX(float rotationX) override;
    virtual void setRotationSkewY(float rotationY) override;
 
    virtual void setAnchorPoint(const Vec2& anchor) override;
    virtual void ignoreAnchorPointForPosition(bool value) override;
 
    virtual void setVisible(bool bVisible) override;
 
    virtual void addChild(Node *child, int zOrder, int tag) override;
    virtual void addChild(Node *child, int zOrder, const std::string &name) override;
    virtual void removeChild(Node* child, bool cleanup) override;
    virtual void removeAllChildrenWithCleanup(bool cleanup) override;
    virtual void reorderChild(Node *child, int zOrder) override;
    virtual void sortAllChildren() override;
 
    virtual void draw(Renderer *renderer, const Mat4 &transform, uint32_t flags) override;
    virtual void setOpacityModifyRGB(bool modify) override;
    virtual bool isOpacityModifyRGB(void) const override;
};
```




