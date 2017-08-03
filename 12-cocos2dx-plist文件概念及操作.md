12-cocos2dx-plist文件概念及操作
====

**1. 什么是plist文件格式**
----

**属性列表（Property List）文件是一种用来存储序列化后的对象的文件，类似于键值对（key-value）的形式**，是一种人类可读的串行化对象文件，
由苹果公司发明，最早用于NeXTSTEP系统。cocos2dx最早由cocos2x-iphone发展而来，因此在引擎中大量采用了此模式。

plist是基于xml的纯文本格式，可以直接用文本编辑器编辑。

**2. cocos2dx中的plist文件**
----

* 图片纹理的配置信息
  将多个纹理小图片打包成一个大图片，并生成plist文件。用于配置各个小图的名称、尺寸大小、以及在
  大图中的所在的矩形区域位置等信息。可以使用TexturePacker工具，将多个小碎图的纹理打包成一张大图片。
  使用 CCSpriteFrameCache 可以载入这类plist文件。
 
* 帧动画的配置信息
  将帧动画的数据信息，生成为plist配置文件。包含每帧间隔、动画重复次数、每一帧所需的图片、每张图片
  的名称、尺寸大小、以及在大图中所在的矩形区域位置等信息，使用 CCAnimationCache 可以载入这类plist文件。

* 粒子特效的配置信息
  将粒子特效的数据信息，生成为plist配置文件。包含粒子发射器的位置信息、发射器模式、最大粒子数量、
  发射角度、发射速度、纹理贴图等等信息。
  
**例(部分代码)：**

plist文件：
 
```
<key>desertExecutioner_0001.png</key>
		<dict>
			<key>aliases</key>
			<array>
			
				<string>desertExecutioner_0002.png</string>
			
			</array>
			<key>spriteColorRect</key>
			<string>{{41, 7}, {66, 84}}</string>
			<key>spriteOffset</key>
			<string>{2, 4}</string>
			<key>spriteSize</key>
			<string>{66, 84}</string>
			<key>spriteSourceSize</key>
			<string>{144, 106}</string>
			<key>spriteTrimmed</key>
			<true/>
			<key>textureRect</key>
			<string>{{780, 68}, {84, 66}}</string>
			<key>textureRotated</key>
			<true/>
		</dict>
```

读取plsit文件：

```
//Executioner.cpp
bool Executioner::init()
{
	if (!Sprite::init())
	{
		return false;
	}
	setMonsterType(EXECUTIONER);
	setName("Executioner_");
	baseSprite = Sprite::createWithSpriteFrameName("desertExecutioner_0001.png");
	addChild(baseSprite);
```

```
//CCSprite.cpp
Sprite* Sprite::createWithSpriteFrameName(const std::string& spriteFrameName)
{
    SpriteFrame *frame = SpriteFrameCache::getInstance()->getSpriteFrameByName(spriteFrameName);
    ...
    return createWithSpriteFrame(frame);
```

```
//CCSpriteFrameCache.cpp
SpriteFrame* SpriteFrameCache::getSpriteFrameByName(const std::string& name)
{
    SpriteFrame* frame = _spriteFrames.at(name);
    ...
    return frame;
```


  
