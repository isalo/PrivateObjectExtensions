PrivateObjectExtensions
---
PrivateObjectExtensions is one wrapper class providing extension methods for [PrivateObject](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.testtools.unittesting.privateobject.aspx). PrivateObjectExtensions allows you to
- get/set private (and any other) fields/properties by simple extension methods
- even if the member is declared in base type

Sample
---
```csharp
public class Base
{
    private string _private = "private member";
    private static string _privateStatic = "private static member";
}

public class Derived : Base
{
}
```
```csharp
[TestMethod]
public void GetMembers()
{
    var derived = new Derived();
    var value1 = derived.GetPrivate("_private");
    var value2 = derived.GetPrivate<string>("_private");
    var value3 = derived.GetPrivate("_privateStatic");
    var value4 = typeof(Base).GetPrivate("_privateStatic");
    // ...
}

[TestMethod]
public void SetMembers()
{
    var derived = new Derived();
    derived.SetPrivate("_private", "changed");
    derived.SetPrivate("_privateStatic", "changed");
    typeof(Base).SetPrivate("_privateStatic", "changed");
    // ...
}
```
- ```GetPrivate()``` is a wrapper of ```PrivateObject.GetFieldOrProperty()``` and ```PrivateType.GetStaticFieldOrProperty()```.
- ```SetPrivate()``` is a wrapper of ```PrivateObject.SetFieldOrProperty()``` and ```PrivateType.SetStaticFieldOrProperty()```.

See more samples in Sample project.

How to Use
---
Simply refer PrivateObjectExtensions project from your unit test project or copy PrivateObjectExtensions.cs to your unit test project.

Why PrivateObjectExtensions?
---
PrivateObject doesn't allow you to access if the member is declared in base type unless you specify that type.
```csharp
// without PrivateObjectExtensions
var targetType = // find the type declaring the member somehow
var po = new PrivateObject(yourObject, new PrivateType(targetType));
po.GetField("_private");
```
Additionally, if you want to access static member, you have to use different way.
```csharp
// without PrivateObjectExtensions
var targetType = // find the type declaring the member somehow
var pt = new PrivateType(targetType);
pt.GetStaticField("_privateStatic");
```
These are totally useless works. What we want to do is just accessing private member simply regardless of it's real type or static. PrivateObjectExtensions automatically find the way to access the member. No need to concern about them all!

This is useful especially when you are mocking by inheriting, for instance using [Moq](https://github.com/moq). Moq mocks an object by inheriting the type. Once we create an instance as a mock object, we have to access private member as above. However, you can simply access by using PrivateObjectExtensions.
