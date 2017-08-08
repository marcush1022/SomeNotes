20-cocos2dx的数学类vec2和Rec
====

1.Rect矩形类
----

矩形类Rect包含两个成员属性：起始坐标（左下角）Vec2、矩阵大小Size。
Rect只对"="运算符进行了重载。

Rect中的主要方法：

```
class CC_DLL Rect
{
public:
    Vec2 origin; //起始坐标: 矩形左下角坐标
    Size  size;  //尺寸大小
 
    //构造函数
    Rect();
    Rect(float x, float y, float width, float height);
    Rect(const Rect& other);
 
 
    //重载"="
    Rect& operator= (const Rect& other);
 
    //设置矩形
    void setRect(float x, float y, float width, float height);
 
    //获取矩形信息
    float getMinX() const; //origin.x
    float getMidX() const; //origin.x + size.width/2
    float getMaxX() const; //origin.x + size.width
 
    float getMinY() const; //origin.y
    float getMidY() const; //origin.y + size.height/2
    float getMaxY() const; //origin.y + size.height
 
    //判断是否与rect相同. 原点相同,尺寸相同.
    bool equals(const Rect& rect) const;
 
    //判断point是否包含在矩形内或四条边上
    bool containsPoint(const Vec2& point) const;
 
    //判断矩形是否相交. 常常用作碰撞检测.
    bool intersectsRect(const Rect& rect) const;
 
    //与rect矩形合并. 并返回结果. v3.0
    //不会改变原矩形的值
    Rect unionWithRect(const Rect & rect) const;
 
    static const Rect ZERO;
 
};
