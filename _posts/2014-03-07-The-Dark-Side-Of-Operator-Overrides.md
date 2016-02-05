---
layout: post
title:  "The Dark Side of Operator Overrides"
date:   2014-02-05 00:17:06 -0700
categories: codecamp
---
This is part 4/10 from my talk “10 Things You Didn't Know About C#”, from Utah Code Camp 2014. (I swear I'll get through these, eventually!)

Owing to its C++ heritage, C# has the ability to override operators in your classes. Commonly, you will see things like the index operator<sup>*</sup> overridden on custom collections, or math operators overridden on numeric classes. Rarely do we ever venture outside the safe, and common operator overrides when writing custom behavior.But there are many more operators than those commonly used. Why should they feel left out?

Lets explore what can be done with some of the less common ones. One of my favorite examples of oddball operators in C# are that of true and false. usually though of as values, C# will allow you to define what it means for your particular object to be true. Let's see what we can do with this behavior:

{% highlight C# %}
void Main()
{
    var boyfriends = new List<Boyfriend>
    {
        new Boyfriend
        {
            LovesMe = true,
            LovesMeNot = false,
            EntriesOnRapSheet = int.MaxValue,
            Name = "Ima Baddude"
        },
    
        new Boyfriend
        {
            LovesMe = false,
            LovesMeNot = true,
            EntriesOnRapSheet = 0,
            Name = "Itsnotyou Itsme"
        },
    
        new Boyfriend
        {
            LovesMe = true,
            LovesMeNot = false,
            EntriesOnRapSheet = 1,
            Name = "Fox Mulder"
        }                
    };
    
    foreach (var marriageCandidate in boyfriends)
    {
        if(marriageCandidate)
        {
            Console.WriteLine(marriageCandidate);
        }
    }
}

public class Boyfriend
{
    public bool LovesMe { get; set; }
    public bool LovesMeNot { get; set; }
    public int EntriesOnRapSheet { get; set; }
    public string Name { get; set; }
    
    public static bool operator true(Boyfriend boyfriend)
    {
        return boyfriend.LovesMe && !boyfriend.LovesMeNot && boyfriend.EntriesOnRapSheet < 5 && boyfriend.Name != "Rico Suave";
    }
     
    public static bool operator false(Boyfriend boyfriend)
    {
        //Simply return the opposite of whatever the true operator returns. 
        //So, if true return false, we return true here - which means "Yes, this is true." Got it?
        
        return !(true); 
    }
              
    public override string ToString()
    {
		return Name;
    }
}
{% endhighlight %}

Rather than checking all of the properties on one object to see if we should continue (which reeks of [feature envy](http://sourcemaking.com/refactoring/feature-envy)), we can encapsulate that behavior into the class itself, and avoid a method call. It certainly makes the code simple and readable. Arguably, it is hiding too much info, because someone unfamiliar with overloading true and false might simply mistake this for a C-style null check on the object instead.

Of course, you can also use this trick for evil. For example, you can make classes that can always be true:

{% highlight C# %}
public class AlwaysTrue
{
    public static bool operator true(AlwaysTrue alwaysTrue)
    {
        return true;
    }
     
    public static bool operator false(AlwaysTrue boyfriend)
    {
        return true;            
    }
}
{% endhighlight %}

Using this operator, we can also attempt to solve an ancient paradox. most succinctly stated as "This statement is false".

{% highlight C# %}
void Main()
{
	var answer = new ThisIsTrue();

	if(answer)
		Console.WriteLine("Paradox ain't nuthin for .NET");
}

public class ThisIsTrue
{
    public static bool operator true(ThisIsTrue statement)
    {
        return statement ? false : true;
    }

    public static bool operator false(ThisIsTrue statement)
    {
        return statement ? true : false;
    }
}
{% endhighlight %}

If you run this, it will stack overflow. Why? Because both false and true are overridden, and we are invoking them directly on the same class we defined them in, it ends in an infinite loop. Its too bad, because I really wanted to know the answer to that paradox.

The only thing left that could make C#'s operator overriding even more open for abuse would be to mutate object state with the operators. Thankfully, all operator overrides are required to be static - so you can't shoot yourself in the foot too badly.

The moral of the story is that there are quite a few C# operators that can be overridden to cut down on boilerplate code, but they need to be overridden responsibly. Make sure to remember the operator and its intended meaning when overriding, and use a method if what you want to do doesn't quite fit the operators meaning. Though, you can take consolation in the fact that [you'll never screw up true and false this badly](http://msdn.microsoft.com/en-us/library/aa432714(v=office.12).aspx).

[Demos &amp; Slides](https://drive.google.com/#folders/0B3kpIc8k4Sb8NGpnTXFGT3hEaE0)

<small><sup>*</sup>Bonus points if you thought "Hey, This guy is wrong!" The index "operator" is not actually a genuine operator that you can override. Rather it is a language feature, which you should be using on custom collections.</small>
