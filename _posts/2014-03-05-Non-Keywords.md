---
layout: post
title:  "(Non) Keywords"
date:   2014-02-05 00:17:06 -0700
categories: codecamp
---
This is part 1/10 from my talk “10 Things You Didn’t Know About C#” from Utah Code Camp 2014.

C# is one of my favorite languages to program for, and .NET is one of my favorite platforms to target. Why? I find the 1-2 punch of C#/.NET to be an amazingly powerful and flexible system.

But, sometimes it can be a little _too_ flexible. Edge cases and emergent behaviors are _everywhere_, and they can provide for some great fun when exploring C#/.NET in depth. With that intro, let’s jump into the first of 10 things you didn’t know: **you can use language keywords as variable names.**

How, you might ask? [According to the C# specification](http://msdn.microsoft.com/en-us/library/x53a06bb.aspx), we can just use the `@` sign in much the same way you would to create [verbatim string literals.](http://msdn.microsoft.com/en-us/library/362314fe.aspx#code-snippet-7) Just prefix the desired keyword with an “@”, and the compiler will happily let you (mis)use keywords as variable names. Check out this example:

{% highlight C# %}
void Main()
{
	var null = "HEY";
	var if = "WHAT R U DOIN";
	var object = "PLZ STAHP";
	
	Console.WriteLine(String.Format("{0} {1} {2}", null, if, object));
}
{% endhighlight %}

It should be noted that this trick does not work with any other variable names - @ can only precede keywords.

I would normally advise against this practice in general, but there are two cases where this might be considered an acceptable choice:

1.  In ASP MVC, it is often useful to set a ‘class’ attribute, which means needing to use ‘class’ as a variable name. (Kudos to whoever pointed this out during the session!)
2.  Use it to alleviate C#’s inability to switch on types. This is only listed to clear my conscience, because I have implemented code like that before. Though I’m still on the fence about actually _deploying_ code that looks like this, it’s still a neat trick:

{% highlight C# %}
void Main()
{
	var @switch = new Dictionary<Type, Action>
	{
		{typeof(CSharp), () =>
			{
				Console.WriteLine("<3");
			}},
		{typeof(Rainbow), () =>
			{
				Console.WriteLine("20% Cooler");
			}},
		{typeof(MtnDew), () =>
			{
				Console.WriteLine("God's Nectar");
			}},
		{typeof(Pencil), () =>
			{
				Console.WriteLine("Like a pen, but you can erase it!");
			}},
	};
	
	//Actually do the switch
	@switch[typeof(Pencil)]();
}
{% endhighlight %}

[Demos & Slides](https://drive.google.com/#folders/0B3kpIc8k4Sb8NGpnTXFGT3hEaE0)
