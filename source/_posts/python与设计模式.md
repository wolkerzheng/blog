---
title: python与设计模式
date: 2017-03-16 14:40:42
tags: python
---
设计模式是面对各种问题进行提炼和抽象而形成的解决方案。这些设计方案是前人不断试验，考虑了封装性、复用性、效率、可修改、可移植等各种因素的高度总结。它不限于一种特定的语言，它是一种解决问题的思想和方法。

设计模式可以分为三个大类：创建类设计模式、结构类设计模式、行为类设计模式。创建类设计模式可以分为单例模式、工厂模式、抽象工厂模式、原型模式、建造者模式；结构类设计模式可以分为装饰器模式、适配器模式、门面模式、组合模式、享元模式、桥梁模式；行为类设计模式可以细分为策略模式、责任链模式、命令模式、中介者模式、模板模式、迭代器模式、访问者模式、观察者模式、解释器模式、备忘录模式、状态模式。
<!--more-->
### 单例模式
单例模式是指：保证一个类仅有一个实例，并提供一个访问它的全局访问点。具体到此例中，总线对象，就是一个单例，它仅有一个实例，各个线程对总线的访问只有一个全局访问点，即惟一的实例。
```
        #encoding=utf8
        import threading
        import time
        #这里使用方法__new__来实现单例模式
        class Singleton(object):#抽象单例
          def __new__(cls, *args, **kw):
              if not hasattr(cls, '_instance'):
                  orig = super(Singleton, cls)
                  cls._instance = orig.__new__(cls, *args, **kw)
              return cls._instance
        #总线
        class Bus(Singleton):
          lock = threading.RLock()
          def sendData(self,data):
              self.lock.acquire()
              time.sleep(3)
              print "Sending Signal Data...",data
              self.lock.release()
        #线程对象，为更加说明单例的含义，这里将Bus对象实例化写在了run里
        class VisitEntity(threading.Thread):
          my_bus=""
          name=""
          def getName(self):
              return self.name
          def setName(self, name):
              self.name=name
          def run(self):
              self.my_bus=Bus()
              self.my_bus.sendData(self.name)

        if  __name__=="__main__":
          for i in range(3):
              print "Entity %d begin to run..."%i
              my_entity=VisitEntity()
              my_entity.setName("Entity_"+str(i))
              my_entity.start()
```

单例模式的优点：
1、由于单例模式要求在全局内只有一个实例，因而可以节省比较多的内存空间；
2、全局只有一个接入点，可以更好地进行数据同步控制，避免多重占用；
3、单例可长驻内存，减少系统开销。
单例模式的应用举例：
1、生成全局惟一的序列号；
2、访问全局复用的惟一资源，如磁盘、总线等；
3、单个对象占用的资源过多，如数据库等；
4、系统全局统一管理，如Windows下的Task Manager；
5、网站计数器。
缺点
1、单例模式的扩展是比较困难的；
2、赋于了单例以太多的职责，某种程度上违反单一职责原则（六大原则后面会讲到）;
3、单例模式是并发协作软件模块中需要最先完成的，因而其不利于测试；
4、单例模式在某种情况下会导致“资源瓶颈”。
