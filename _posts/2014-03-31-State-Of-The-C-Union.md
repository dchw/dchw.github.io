---
layout: post
title:  "State Of The C# Union"
date:   2014-03-31 00:17:06 -0700
categories: codecamp
---
This is part 6/10 from my talk “10 Things You Didn’t Know About C#”, from Utah Code Camp 2014.

Another piece of its C++ heritage, C# can also create unions, with almost all of the exact same functionality you would expect! By making use of `[StructLayout]` and `[FieldOffset]`, you can pack structs however you would like. Here is a sample struct packed like this:

{% highlight C# %}
[StructLayout(LayoutKind.Explicit)]
struct Unioned
{
	[FieldOffset(0)]
    public long l;
    
	[FieldOffset(0)]
    public short hi;
    
	[FieldOffset(sizeof(short))]
    public short low;
	
	[FieldOffset(0)]
	public byte c0;
	
	[FieldOffset(sizeof(byte))]
	public byte c1;
	
	[FieldOffset(2*sizeof(byte))]
	public byte c2;
	
	[FieldOffset(3*sizeof(byte))]
	public byte c3;
	
	[FieldOffset(4*sizeof(byte))]
	public byte c4;
	
	[FieldOffset(5*sizeof(byte))]
	public byte c5;
	
	[FieldOffset(6*sizeof(byte))]
	public byte c6;
	
	[FieldOffset(7*sizeof(byte))]
	public byte c7;
	
	public byte[] AsByteArray()
	{
		return new []{c7, c6, c5, c4, c3, c2, c1, c0};
	}
}
{% endhighlight %}

In this example, The first field is a `long`, but I can get the first byte and last bytes individually if I would like. The remaining chars are packed individually afterward, and I can access those as expected.

The packing of each of the chars(c1-c7) afterward seems odd, though - why would I need to specify the packing of all parts in the struct? This is because the value I passed to the [StructLayout] attribute requires that all packing and positioning be specified for each field.

[Demos & Slides](https://drive.google.com/#folders/0B3kpIc8k4Sb8NGpnTXFGT3hEaE0)
