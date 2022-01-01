---
layout: single
title:  Locate .NET Assembly for a given type in X++ 
tags:                                             
  - D365FO
categories:
 - X++ 
---

I was experiencing an error in a standard Dynamics 365 F&O class that source code has not available.

I was experiencing an error in a standard Dynamics 365 F&O class that source code has not available.  I needed to understand class internal behavior by analyzing source code. 

To decompile an assembly using [ILSpy](https://github.com/icsharpcode/ILSpy) or [Jet Brains dotPeek](https://www.jetbrains.com/decompiler/) is required to know the assembly file path in the file system.  

Some objects are not straightforward to get matching assembly file. 

Instead of browsing and expanding assemblies one by one, there is a quick way of getting that information. There is an easy way to get assembly for a class/table, etc.

````csharp
public static void Main(Args _args)
{
    System.Object               obj;
    System.Type                 type;
    System.Reflection.Assembly  assembly;
    
    var cache = new SysGlobalObjectCache();
    type = cache.GetType();
    assembly = type.Assembly;
    
    info(assembly.FullName);
    info(assembly.Location);
    info(type.FullName);

}
````

Output

![!](/assets/images/posts/2022/01/20220101_01.png)

This code also works for tables and other data types. For example, for Customer Table. 
Just cast table to System.Object. 

````csharp
public static void Main(Args _args)
{
    System.Object               obj;
    System.Type                 type;
    System.Reflection.Assembly  assembly;
    
    CustTable custTable;
    obj = custTable;
    type = obj.GetType();
    assembly = type.Assembly;
    
    info(assembly.FullName);
    info(assembly.Location);
    info(type.FullName);
    
}
````

![!](/assets/images/posts/2022/01/20220101_02.png)

Feel free to leave a comment on twitter.