---
layout: post
title:  "Rounding Ain't So Round"
date:   2014-02-07 00:17:06 -0700
categories: codecamp
---
This is part 3/10 from my talk "10 Things You Didn't Know About C#", from Utah Code Camp 2014.

Remember learning how to round in elementary school? Most of us learned that the decimal rounds to the nearest whole number, and that half always rounds away from zero.

Interestingly, this is not what C# does by default, and being unaware of it might cause unexpected results.

**Bankers Rounding**

C#'s default method for rounding is called [half-to-even rounding](http://en.wikipedia.org/wiki/Rounding#Round_half_to_even), or sometimes "Banker's Rounding". True to the name, this rule states that half will always round to the nearest _even_ number. This type of rounding is more precise over many calculations because it eliminates the bias that rounding away from zero creates. This bias-eliminating property is one of the reasons this type of rounding is [recommended in the IEEE 754 standard for rounding in floating-point numbers](http://en.wikipedia.org/wiki/IEEE_floating_point#Rounding_rules).

**Decimal Truncation**

There is one other type of rounding you need to know about from the IEEE spec. It is known as Round-toward-zero, but you might know it as truncation. This type of rounding simply removes any decimal, producing a whole number, and is always biased toward zero.

**Getting Expected Results**

What if you _want_ to round the way you are used to? Fortunately, Microsoft has you partially covered. The [`System.MidpointRounding` enum](http://msdn.microsoft.com/en-us/library/system.midpointrounding.aspx) lists both `AwayFromZero`, and `ToEven` as valid rounding types.

`Math.Round()` will let you round however you want, but by default will apply Half-to-even rounding.

`Convert.ToInt32()` and variants _do not give you the luxury of choosing your rounding type_, and will always apply Half-to-even rounding.

Lets look at some comparisons. Here is the code:

{% highlight C# %}
var numsToRound = new []{0.5, 1.5, 2.5, 3.5, 4.5};

Console.WriteLine("Banker|Away|Convert|Cast");
foreach(var num in numsToRound)
{
    var toEven = Math.Round(num);
    var awayFromZero = Math.Round(num, MidpointRounding.AwayFromZero);
    var convert = Convert.ToInt32(num);
    var cast = (int)num;
    Console.WriteLine(String.Format("  {0}   | {1}  |   {2}   | {3}  ", toEven, awayFromZero, convert, cast));
}
{% endhighlight %}

And the output:

{% highlight C# %}
Banker|Away|Convert|Cast
  0   | 1  |   0   | 0  
  2   | 2  |   2   | 1  
  2   | 3  |   2   | 2  
  4   | 4  |   4   | 3  
  4   | 5  |   4   | 4  
{% endhighlight %}

Take note that we have _two_ different answers for any index, and _three_ different lists!

Moral of the story: Be careful in how you choose to handle your decimals. Accuracy matters!

[Demos & Slides](https://drive.google.com/#folders/0B3kpIc8k4Sb8NGpnTXFGT3hEaE0)
