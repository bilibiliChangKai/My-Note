# objective-c特殊特性

> Author: huzi<br>Date: 19-1-7 11:19

---

1. id：动态类型，万能指针，可视为void*。

   ~~~objective-c
   #import <Foundation/Foundation.h>
   #import "Cat.h"
   #import "Animal.h"
   
   int main(int argc, const char * argv[]) {
       @autoreleasepool {
           Animal *cat = [Cat new];
           [(Cat *)cat jump];
   
           NSObject *obj=[Cat new];
           [(Cat*)obj jump];
   
           //编译器对NSObject做类型检测，但是不对id做类型检测
           //所以id不用强制类型转换也可以运行成功，编译也报警告。
           id c=[Cat new];
           [c jump];
       }
   
   
       return 0;
   }
   ~~~

2. 一些动态类型检测方法：

   最好用：

   - isKindOfClass
   - respondsToSelector（要用selector）

   ~~~objective-c
   #import <Foundation/Foundation.h>
   #import "DogSon.h"
   
   int main(int argc, const char * argv[]) {
       @autoreleasepool {
           //1.判断对象是不是指定类对象或者指定类的子类对象
           Class dog=[Dog class];
           DogSon *son=[DogSon new];
           BOOL res=[son isKindOfClass:dog];
           NSLog(@"iskindOfClass：%d",res);
   
           //2.判断对象是不是1个特定类型的对象，不包括子类，父类
           Class dog1=[Dog class];
           DogSon *son1=[DogSon new];
           res=[son1 isMemberOfClass:dog1];
           NSLog(@"isMemberOfClass：%d",res);
   
           //3.判断1个类是不是另外一个类的子类
           Class dog2=[Dog class];
           res=[DogSon isSubclassOfClass:dog2];
           NSLog(@"isSubclassOfClass：%d",res);
   
           //4.判断对象中是否能响应指定的方法，这个最常用
           SEL sel=@selector(play);
           Dog *son2=[DogSon new];
           res=[son2 respondsToSelector:sel];
           NSLog(@"respondsToSelector：%d",res);
   
           //5.判断类中是否能响应指定方法
           sel=@selector(play);
           res=[Dog instancesRespondToSelector:sel];
           NSLog(@"respondsToSelector：%d",res);
       }
       return 0;
   }
   ~~~

3. category：分类，类似于一种类的扩展

   - 不能定义私有变量

   - 调用方法相同，分类方法会覆盖子类方法。
   - 子类能调用父类扩展方法。

   基类：

   ~~~objective-c
   // Person.h
   #import <Foundation/Foundation.h>
   
   @interface Person : NSObject
   {
       @protected
       int _age;
       @public
       int _sex;
       @private
       int _num;
   }
   @property (nonatomic,retain)NSString *name;
   @end
       
   // Person.m
   #import "Person.h"
   
   @implementation Person
   
   @end
   ~~~

   分类：

   ~~~objective-c
   // Person+eat.h
   #import "Person.h"
   
   @interface Person (eat)
   {
      // int eat;//分类中不能定义成员变量
   }
   //会生成set和get方法，但是不会生成带_的成员变量，不建议写
   //@property (nonatomic,assign)int age;
   - (void) eatFish;
   
   @end
       
   // Person+eat.m
   #import "Person+eat.h"
   
   @implementation Person (eat)
   
   -(void)eatFish{
       _age=10;
   
       NSLog(@"我在吃%d只鱼",_age);
   }
   @end
   ~~~

4. 类扩展：类似于c#中的particle，但是限制更多。

   - 在.m中定义类扩展是私有的(在.h上是公有的)，不能被子类和外部访问

   ~~~objective-c
   // Person.h
   #import <Foundation/Foundation.h>
   
   @interface Person : NSObject
   {
       @protected
       int _age;
       @public
       int _sex;
       @private
       int _num;
   }
   @property (nonatomic,retain)NSString *name;
   @end
       
   // Person.m
   /**
    类创建的第二种方式：@interface 类名()
                     里面可以声明成员变量
                    里面可以声明方法，类扩展声明的方法，必须实现
                    @end
    这种创建类扩展的方式，是真正的私有
    这种私有，子类无法通过任何方式访问父类中类扩展的方法和成员变量
    */
   
   #import "Person.h"
   //这种在.m中创建的类扩展，最常用,真正私有
   @interface Person()
   {
       NSString *_name;
       int _age;
   }
   @property (nonatomic,assign)int _sex;
   
   -(void)show;
   
   @end
   
   
   @implementation Person
   -(void)show{
       _age=20;
       _name=@"张三";
       NSLog(@"%d的%@在泡泡跑",_age,_name);
   }
   @end
   ~~~


