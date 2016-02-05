---
layout: post
title:  "Enumerated Tricks"
date:   2014-02-05 00:17:06 -0700
categories: codecamp
---
This is part 2/10 from my talk “10 Things You Didn’t Know About C#”, from Utah Code Camp 2014.

When I first began seriously programming; I learned Java. Java had some really cool features baked into its implementation of enums - like methods, and enum values of arbitrary types. When I switched to C# as my primary language, most of that flexibility was gone - and I missed it.

However, after much searching, I have found that there are ways to get some of that Java-like flexibility back.

**Backing Types**

In stark contrast to Java, C# only has [8 possible backing types](http://msdn.microsoft.com/en-us/library/sbbt4032.aspx) that can be used in enums. However, this simplicity can let us do some really cool things.

One trick that the language specifies for us is the [`[Flags]` attribute](http://msdn.microsoft.com/en-us/library/system.flagsattribute.aspx), seen in action below:

{% highlight C# %}
[Flags]
public enum Powers : byte
{
    Speed = 1,
    Looks = 1 << 1,
    Money = 1 << 2,
    Smart = 1 << 3,
    None  = 1 << 4
}
{% endhighlight %}

By specifying this flag, we tell the compiler to treat each value as a component of a bit field. Most times, we want each bit in the field to represent a particular flag being set, and therefore manually specify each value to be a power of two.

To me, this breaks the continuity of an enum. As seen above, you can use simple left shifts to get the compiler to do the work for you, and maintain a nice, continuous numbering scheme. It also makes re-ordering bits in the field much easier.

As a consequence of C#’s bit field abilities, it should also be noted that it is possible to assign any valid value of the enum’s backing type to an enum variable. Whoa, that sentence was a mouthful. Here’s what I mean:

{% highlight C# %}
var hero = (Superhero)42;
var powers = Powers.Looks | Powers.Money | Powers.Smart
{% endhighlight %}

Note how both assigned values are specified explicitly by the enums above. For bit fields, this is necessary to represent combinations of enum flags together. But, it also applies when the enum is _not_a bit field, allowing arbitrary, un-enumerated values to be assigned via casting.

**Enums Are Types, Too**

Because enums are types in their own respect, it means that we can also write extension methods for them. This lets us almost get Java-like methods for our enums. See below:

{% highlight C# %}
public static class SuperheroEx
{
    public static string Name(this Superhero superhero)
    {
        return Enum.GetName(typeof(Superhero), superhero);
    }
}
{% endhighlight %}

You can get incredible functionality from these, when applied in the right circumstances. In this case, I simply use it to determine the superheroes name.

**Tying it all together**

Lets see what using each of these together might look like:

{% highlight C# %}
public static class SuperheroEx
public class HeroWithPowers
{
	public Superhero Hero {get; set;}
	public Powers Powers {get; set;}
}

void Main()
{
    var heros = new List<HeroWithPowers>
    {
        new HeroWithPowers
        {
            Hero = Superhero.IronMan,
            Powers = Powers.Looks | Powers.Money | Powers.Smart
        },
        new HeroWithPowers
        {
            Hero = Superhero.Aquaman,
            Powers = Powers.None
        },
    };

    foreach(var hero in heros)
    {
        Console.WriteLine("Name: " + hero.Hero.Name());
        Console.WriteLine("Powers:");
        
        if(hero.Powers.HasFlag(Powers.Speed)) Console.WriteLine("SPEED");
        if(hero.Powers.HasFlag(Powers.Looks)) Console.WriteLine("LOOKS");
        if(hero.Powers.HasFlag(Powers.Money)) Console.WriteLine("$$$$$");
        if(hero.Powers.HasFlag(Powers.Smart)) Console.WriteLine("SMART");
        if(hero.Powers.HasFlag(Powers.None))  Console.WriteLine(":(");
        
        Console.WriteLine();
    }
}
{% endhighlight %}

These enums almost look like regular classes from here! Mission accomplished.

It should also be noted that the `HasFlag()` method is new in C# 4.0. In earlier versions, you still have to use to determine if it is set.

[Demos &amp; Slides](https://drive.google.com/#folders/0B3kpIc8k4Sb8NGpnTXFGT3hEaE0)
