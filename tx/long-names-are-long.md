# long-names-are-long

> Author: huzi(moustache)<br>Date: 18-8-7 10:14

类型：文章

地址：http://journal.stuffwithstuff.com/2016/06/16/long-names-are-long/

# 总结

减少变量名长度的四个原则：

1. 变量类型可以表示一定的意义，没必要包含在变量名中。

   举例：

   ~~~
   string nameString; => string name;
   List<DateTime> holidayDateList; => List<DateTime> holidays;
   ~~~

2. 变量自己的属性如果无法用少量单词描述清楚的话，可以不加。

   举例：

   ~~~
   finalBattleMostDangerousBossMonster; => boss;
   ~~~

3. 变量定义的时候都有自己的语境，语境不需要重复描述。

   举例：

   ~~~
   class AnnualHolidaySale {
     int _annualSaleRebate;
     void promoteHolidaySale() { ... }
   }
   =>
   class AnnualHolidaySale {
     int _rebate;
     void promote() { ... }
   }
   ~~~

4. 一些写了不写的属性可以除去，比如object，entity，amount，state之类的。

   举例：

   ~~~
   class WaffleObject {
     void garnish(List<Strawberry> strawberries) { ... }
   }
   =>
   class Waffle {
     void garnish(List<Strawberry> strawberries) { ... }
   }
   ~~~

   

