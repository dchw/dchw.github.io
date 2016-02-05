---
layout: post
title:  "Casting Magic"
date:   2014-02-05 00:17:06 -0700
categories: codecamp
---
This is part 5/10 from my talk “10 Things You Didn’t Know About C#”, from Utah Code Camp 2014.

In most statically typed languages, there is the notion of casting, which can be used to convert values from one type to another. These casts take on two forms: _implicit_ (when the cast is inferred by the variable's usage), and _explicit_ (when you manually specify a conversion). However, in C# there is almost-kinda-sorta way to create a 3rd kind of cast - which I have seen described as "Casting by Example".

**Casting By Example**

What is casting by example? At its core, it is a way you can exploit .NET's built-in type caching mechanism, generics, and the var keyword to re-use anonymous types, even outside of the method they were declared in! Sounds gross right? It its! There is no good reason to _ever_ use this! But, its really cool, so lets play with the fire:

{% highlight C# %}
public static class ObjectExtensions
{
    public static T CastByExample<T>(this object o, T example) 
    { 
        return (T) o; 
    }
}

void Main()
{
    var anonymousObject = Mystery.Enigma();
    var unAnonymousObject = anonymousObject.CastByExample(new
    {
        Message = String.Empty,
        Fruit = String.Empty,
    });
	
    Console.WriteLine(unAnonymousObject.Message);
    Console.WriteLine(unAnonymousObject.Fruit);
}

public class Mystery
{
    public static object Enigma()
    {
    	return new
        {
            Message = "Anonymous type!",
            Fruit = "Mango"
        };
    }
}
{% endhighlight %}

What is going on here? Well, in `Main()`, we call `Mystery.Enigma()`, which returns an anonymous type. Under the hood, the first time .NET sees this anonymous type, it generates a class for you - and all future anonymous objects of this type will be of the same object type (this is also why anonymous types are [`IEquitable`](http://msdn.microsoft.com/en-us/library/ms131187.aspx)). This is especially handy when creating a collection of an anonymous type, and means that the framework only has one anonymous type to deal with.

The real magic happens in the extension method `CastByExample()`. By passing in an example `T` that _looks exactly the same_ as the one generated in `Mystery.Enigma()`, .NET simply casts our object to the earlier generated type. And, because we are using var, we never really have to know the actual type name!

It should be noted that "_looks exactly the same_" means that _both_ the field names and types are the same - otherwise .NET will generate a new type, and using this extension method would fail at runtime.

Thanks to Alex D. James, over at MSDN for [publishing this cool trick 7 years ago](http://blogs.msdn.com/b/alexj/archive/2007/11/22/t-castbyexample-t-object-o-t-example.aspx)!

[Demos & Slides](https://drive.google.com/#folders/0B3kpIc8k4Sb8NGpnTXFGT3hEaE0)
