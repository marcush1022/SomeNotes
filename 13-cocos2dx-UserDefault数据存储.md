13-cocos2dx-CCUserDefault数据存储
====

cocos2dx中的数据存储类为CCUserDefault，可作为轻量级数据库使用。<br>
在3.0中类名去掉了CC。

CCUserDefault与CCDirector和SimpleAudioEngine一样，都采用单例模式。

**CCUserDefault采用了XML的存储技术，类似map的映射，键与值一一对应，可以通过set(), get()方法修改值**
首次使用时xml并不存在，CCUserDefault会自动创建相应的xml文件.<br>
支持5种数据：bool, init, float, double, string.<br>

**CCUserDefault中的方法**

**1. set/get 设置/获取数据值**

```
    //设置bool, int, float, double, string
    void setBoolForKey(   const char* pKey, bool value);               
    void setIntegerForKey(const char* pKey, int value);                
    void setFloatForKey(  const char* pKey, float value);              
    void setDoubleForKey( const char* pKey, double value);             
    void setStringForKey( const char* pKey, const std::string& value);
```

```
//通过关键字，来从XML文件中获取数据值
//若关键字不存在，会自动设置键值映射，并设置对应的默认值defaultValue
    //获取bool, int, float, double, string
    bool getBoolForKey(   const char* pKey, bool defaultValue = false);                   
    int getIntegerForKey(const char* pKey, int defaultValue = 0);                        
    float getFloatForKey(  const char* pKey, float defaultValue = 0.0);                 
    double getDoubleForKey( const char* pKey, double defaultValue = 0.0);               
    std::string getStringForKey( const char* pKey, const std::string& defaultValue = ""); 
```

**2. flush() 保存数据**

通过setXXXForKey()设置完数据之后需要调用flush()函数保存数据

**3. 其他**

```
    static CCUserDefault* sharedUserDefault();  //获取单例对象
    static void purgeSharedUserDefault();       //释放单例对象
    const static std::string& getXMLFilePath(); //获取XML路径
    static bool isXMLFileExist();               //XML文件是否创建
```

**例：**

```
//slotMenu.cpp
//读取存档数据 
  if(UserDefault::getInstance()->getIntegerForKey(String::createWithFormat("Slot%d",getNum())->getCString(),0)==0)
	{
		createNewGame();
		button_Savelot_Delete->setVisible(false);
```




    
 

