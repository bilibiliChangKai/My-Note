# Linq有用方法总结

> Author：huzi(moustache)<br>Date：18-12-21 16:21

## Linq介绍

Linq是C#2.0编写的一个库，通过using System.Linq引用，里面包含了很多新的特性和新的函数式方法，比如map之类的。在工作中，会遇到想用但是不知道怎么用的这种情况，因此本文中总结了一些Linq中用到的方法供以后查阅。

## 有用特性

1. 匿名类型：

   ~~~c#
   var obj = new {Guid.Empty, myTitle = "匿名类型", myOtherParam = new int[] { 1, 2, 3, 4 } };
    Console.WriteLine(obj.Empty);  //另一个对象的属性名字，被原封不动的拷贝到匿名对象中来了。
    Console.WriteLine(obj.myTitle);
    Console.ReadKey();
   ~~~

   obj是匿名类型，实际上是Anonymous Type。

   不能在方法外面访问这个类型的属性。

2. 自动属性：

   ~~~c#
   public class MyObj
   {
       public Guid id { get; set; }
       public string Title { get; set; }
   }
   
   // 等同于
   
   public class MyObj
   {
       private Guid _id;
       private string _Title;
       public Guid id 
       {
           get { return _id; }
           set { _id = value; } 
       }
       public string Title
       {
           get { return _Title; }
           set { _Title = value; }
       }
   }
   ~~~

3. Action和Func委托

   ~~~c#
   Action<T1, T2, T3> <=> delegate void Undefined_Name(T1, T2, T3);
   Func<T1, T2, T3> <=> delegate T3 Undefined_Name(T1, T2);
   ~~~

4. 扩展方法

   ~~~c#
   // in static class
   public static void PrintString(this String val)
   {
       Console.WriteLine(val);
   }
   
   // use
   var a = "aaa";
   a.PrintString();
   Console.ReadKey();
   ~~~

## 有用API

1. Where：等同于filter

   ~~~c#
   List<int> arr = new List<int>() { 1, 2, 3, 4, 5, 6, 7 };
   arr.Where(a => a > 3)；  // {4, 5, 6, 7}
   ~~~

2. Select：等同于map

   ~~~c#
   List<int> arr = new List<int>() { 1, 2, 3 };
   arr.Select<int, string>(a => a.ToString());  //  { "1", "2", "3"}
   ~~~

3. All，Any，Contains：意思同函数名

4. Skip，SkipWhile：等同于tail

   ~~~c#
   List<int> arr = new List<int>() { 1, 2, 3, 4, 5, 6, 7 };
   arr.Skip(2)；  // {4, 5, 6, 7}
   arr.SkipWhile(a => a <= 4);  // {5, 6, 7}
   ~~~

5. Take，TakeWhile：等同于front

   ~~~c#
   List<int> arr = new List<int>() { 1, 2, 3, 4, 5, 6, 7 };
   arr.Take(2)；  // {1, 2, 3}
   arr.TakeWhile(a => a <= 4);  // {1, 2, 3, 4}
   ~~~

6. Concat：意思同函数名

7. Count：等同于count

   ~~~c#
   List<int> arr = new List<int>() { 1, 2, 3, 4, 5, 6, 7 };
   arr.Count(a => a > 3);  // 4
   ~~~

8. Max，Min：意思同函数名

   ~~~c#
   Pet[] pets = { new Pet { Name="Barley", Age=8 },
                  new Pet { Name="Boots", Age=4 },
                  new Pet { Name="Whiskers", Age=1 } };
   pets.Max(pet => pet.Age + pet.Name.Length);  // 14
   pets.Min(pet => pet.Age + pet.Name.Length);  // 9
   ~~~

