---
title: "Cool stuff with Formatted Text"
layout: post
date: 2017-03-10
<!-- image: /assets/images/markdown.jpg -->
headerImage: false
tag:
- xamarin
- xamarin.forms
- c#
category: blog
author: yurababiy
description: Various ways of creating and real-life use.
---

## Summary:
Article is comprehensive, so please use anchors below to navigate between
sections.

- [Introduction](#Introduction)
- [How to work with font icons](#How to work with font icons)
- [Understanding of FormattedText](#Understanding of FormattedText)
- [Combo of Formatted Text and Font Icons](#Combo of Formatted Text and Font Icons)
- [Some useful things](#Some useful things)
- [Conclusion](#Conclusion)

---  

## Introduction:
We start from custom fonts and font icons.
There are plenty of articles on internet, where you can find how to use custom
font on various platforms. It is good for style your text, but what cool comes
with custom fonts, is font icons. It has many advantages over icons as images,
among which the main one are: *scalability*, *small size*, *flexibility*.  

One of the most popular icon fonts is [Font Awesome](http://fontawesome.io/icons/).
It's amazing and it's free. So let's do something cool with it.

## How to work with font icons:
First, [download](http://fontawesome.io/) font file. It could be `.ttf` or `.otf`,
but for now it is better to use `.otf` because file name is "FontAwesome".
Be sure that you don't change it's name, because it has internal name, which you can
see after opening it. This internal name is being used by Xamarin.
Now, add file to platform projects. I only write my apps for iOS and Android, so
please forgive me that I don't provide information for other platforms.

**iOS**: *Resources folder -> Add -> Existing item -> FontAwesome.otf.*  
Be sure that:    
 - Build Action set to *BundleResource*;  
 - Copy to Output Directory set to *Copy Always*;  

**Android**: *Assets folder -> Add -> Existing item -> FontAwesome.otf.*  
Be sure that:  
 - Build Action set to *AndroidAsset*;  

For iOS you also should add some lines to `Info.plist`. Please open this file with
Xml Editor and add this:
{% highlight Xml %}
<key>UIAppFonts</key>
<array>
  <string>FontAwesome.otf</string>
</array>
{% endhighlight %}

For Android you should add some lines of code to make it
work. Create empty custom class in that derives from `Label`, in shared project:

{% highlight C# %}
public class FontIcon : Label { }
{% endhighlight %}

And then add custom renderer in Android project:

{% highlight C# %}
[assembly: ExportRenderer(typeof(FontIcon), typeof(FontIconRenderer))]
namespace CoolStuffWithFormattedText.Droid
{
    public class FontIconRenderer : LabelRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Label> e)
        {
            base.OnElementChanged(e);

            if ( e.OldElement == null )
            {
                Control.Typeface = Typeface.CreateFromAsset(Forms.Context.Assets, Element.FontFamily + ".ttf");
            }
        }
    }
}
{% endhighlight %}

If it's `.ttf`, then OK, but because of `.otf` you should modify it to avoid error on Android:

{% highlight C# %}
base.OnElementChanged(e);
if (Control == null || Element.FontFamily == null) return;

if ( e.OldElement == null )
{
   try
   {
       Control.Typeface = Typeface.CreateFromAsset(Forms.Context.Assets, Element.FontFamily + ".ttf");
   }
   catch ( Java.Lang.RuntimeException exception )
   {
       if ( exception.Message == "native typeface cannot be made")
           Control.Typeface = Typeface.CreateFromAsset(Forms.Context.Assets, Element.FontFamily + ".otf");
       else
           throw;
   }
}
{% endhighlight %}

Now, we can use our FontIcon in code or XAML. Do it like that:

{% highlight XML %}
 xmlns:coolStuffWithFormattedText="clr-namespace:CoolStuffWithFormattedText;assembly=CoolStuffWithFormattedText"

<coolStuffWithFormattedText:FontIcon Text="&#xf19d;"
                                     FontFamily="FontAwesome"
                                     TextColor="Maroon"
                                     FontSize="24"/>

{% endhighlight %}

In C# your unicode should be like this: `\uf19d`. All unicodes for    
 Font Awesome
you can find [here](http://fontawesome.io/cheatsheet/).


## Understanding of  FormattedText:

I think Formatted Text should be more covered by official resources, because it's
powerful thing and it has many ways of usage. But what is Formatted Text?  
`FormattedText` is a property of `Label`. It's like text, where every piece of it
can be styled almost independently. You can create it in XAML, or from code. It
is bindable, and it is where interesting stuff.
In XAML:
{% highlight XML %}
<Label>
     <Label.FormattedText>
       <FormattedString>
         <FormattedString.Spans>
           <Span Text="Red, "
                 ForegroundColor="Red"
                 FontAttributes="Italic"
                 FontSize="20" />
           <Span Text=" blue, "
                 ForegroundColor="Blue"
                 FontSize="32" />
           <Span Text=" and green! "
                 ForegroundColor="Green"
                 FontSize="12"/>
         </FormattedString.Spans>
       </FormattedString>
     </Label.FormattedText>
   </Label>
{% endhighlight %}
Very disappoint is that `Text` in `Span`  isn't bindable. And here is where bindable
`FormattetText` is ruling. From code:

{% highlight XML %}
<Label FormattedText="{Binding CustomText}"/>
{% endhighlight %}

{% highlight C# %}
private string first = "First";
private string third;

public FormattedString CustomText => new FormattedString
{
    Spans =
    {
        new Span {Text = first, FontAttributes = FontAttributes.Italic, FontSize = 18},
        new Span {Text = "Second"},
        new Span {Text = third, ForegroundColor = Color.Green, BackgroundColor = Color.Gray}
    }
};

public FormattedTextPageViewModel()
{
    third = "Third";
}
{% endhighlight %}

More cool that we can add or edit spans dynamically:
{% highlight XML %}
<Label FormattedText="{Binding DynamicFormattedText}"/>

    <Button Command="{Binding AddSpanCommand}" Text="Add Span" HorizontalOptions="FillAndExpand"/>
{% endhighlight %}

{% highlight C# %}
private FormattedString _dynamicFormattedText = new FormattedString { Spans = { new Span { Text = "0", FontSize = 18 } } };
public FormattedString DynamicFormattedText
{
    get { return _dynamicFormattedText; }
    set { SetProperty(ref _dynamicFormattedText, value); }
}

public DelegateCommand AddSpanCommand => new DelegateCommand(OnAddSpan);

private void OnAddSpan()
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if ( DynamicFormattedText.Spans.Count >= 1 )
        {
            DynamicFormattedText.Spans[DynamicFormattedText.Spans.Count - 1].Text = "1";
            DynamicFormattedText.Spans[DynamicFormattedText.Spans.Count - 1].ForegroundColor =
                Color.FromHex($"#{new Random().Next(0x1000000):X6}");
        }
        var span = new Span { Text = "0", FontSize = 18 };
        DynamicFormattedText.Spans.Add(span);
    });

}
{% endhighlight %}
<!-- markup clean_ -->
Maybe you will come to thought that you could declare your span once and not
create it all the time. It is bad idea. **`Span` is reference type**, so you will
change all the spans when you want to change one.  
Still don't get the idea what benefits of it? Don't worry, next chapter will open
your eyes.

## Combo of Formatted Text and Font Icons:
Before we start to take all advantages of it, it would be better to add some
that will save you time on debugging one bug, that occurs in Forms implementation
of `FormattedText` in `Label`. It sometimes mess up with `FontFamily` of particular
spans.  
Therefore, first, create custom render of `Label`. I would suggest you to create
for it derived class from `Label`.
{% highlight C# %}
public class CustomSpannableLabel : Label { }
{% endhighlight %}
Second, create custom render for iOS and Android.
For iOS it has about 100 lines of code, so I will post here only meaningful code:
{% highlight C# %}
private void UpdateFormattedText()
{
    var text = Control?.AttributedText as NSMutableAttributedString;
    if ( text == null ) return;
    var fontFamily = Element.FontFamily;
    text.BeginEditing();
    FixBackground(text);
    if ( Element.FormattedText == null )
        FixFontAtLocation(0, text, fontFamily, Element.FontAttributes);
    else
    {
        var location = 0;
        foreach ( var span in Element.FormattedText.Spans )
        {
            var spanFamily = span.FontFamily ?? fontFamily;
            FixFontAtLocation(location, text, spanFamily, span.FontAttributes);
            location += span.Text.Length;
        }
    }
    text.EndEditing();
}

private void FixFontAtLocation(int location, NSMutableAttributedString text, string fontFamily,
   FontAttributes fontAttributes)
{
   if ( fontFamily == null ) return;

   NSRange range;
   var font = ( UIFont )text.GetAttribute(UIStringAttributeKey.Font, location, out range);
   var newName = GetFontName(fontFamily, fontAttributes);
   font = UIFont.FromName(newName, font.PointSize);
   text.RemoveAttribute(UIStringAttributeKey.Font, range);
   text.AddAttribute(UIStringAttributeKey.Font, font, range);
}
{% endhighlight %}
What it does, it's setting font family for each span.
For Android we should add a code with pretty same functionality:
{% highlight C# %}
if ( Element?.FormattedText == null ) return;

            var extensionType = typeof(FormattedStringExtensions);
            var type = extensionType.GetNestedType("FontSpan", BindingFlags.NonPublic);
            var ss = new SpannableString(Control.TextFormatted);
            var spans = ss.GetSpans(0, ss.ToString().Length, Class.FromType(type));
            foreach ( var span in spans )
            {
                var font = ( Font )type.GetProperty("Font").GetValue(span, null);
                if ( ( font.FontFamily ?? Element.FontFamily ) != null )
                {
                    var start = ss.GetSpanStart(span);
                    var end = ss.GetSpanEnd(span);
                    var flags = ss.GetSpanFlags(span);
                    ss.RemoveSpan(span);
                    var newSpan = new CustomTypefaceSpan(Control, Element, font);
                    ss.SetSpan(newSpan, start, end, flags);
                }
            }
            Control.TextFormatted = ss;
{% endhighlight %}
We should say thanks [Michael](http://smstuebe.de/2016/04/03/formattedtext.xamrin.forms/) for this fix.  
To see all code I attach a link on this project in the end of the article.  
Now with this, we can create something *really good*. It depends on your imagination how you will use it. It's easy to create a ***checkbox***, or ***radio button***, ***stars***, ***likes*** and ***everything*** you like.  
I have created something like battery status control to display you it's possibilities.
{% highlight XML %}
<StackLayout HorizontalOptions="CenterAndExpand"
               VerticalOptions="CenterAndExpand">
    <coolStuffWithFormattedText:CustomSpannableLabel FormattedText="{Binding Battery}"
                                                     HorizontalOptions="FillAndExpand"
                                                     HorizontalTextAlignment="Center"
                                                     VerticalOptions="CenterAndExpand"/>
    <Button Command="{Binding ConsumeBatteryCommand}"
            Text="Consume Battery"
            HorizontalOptions="CenterAndExpand"
            VerticalOptions="CenterAndExpand"/>
  </StackLayout>
{% endhighlight %}  
{% highlight C# %}
private FormattedString _battery = new FormattedString
{
    Spans =
    {
        new Span { Text = BatteryStatusIcons[0], FontSize = 18, FontFamily = "FontAwesome", ForegroundColor = Color.Green },
        new Span { Text = BatteryStatus[0], FontSize = 18, ForegroundColor = Color.Black }
    }
};

public FormattedString Battery
{
    get { return _battery; }
    set { SetProperty(ref _battery, value); }
}

private static readonly string[] BatteryStatusIcons = { "\uf240", "\uf241", "\uf242", "\uf243", "\uf244" };
private static readonly Color[] BatteryColors = { Color.Green, Color.Yellow, Color.Olive, Color.Red, Color.Black };
private static readonly string[] BatteryStatus = { " Full", " Half", " Low", " Very low", " Dead" };
public DelegateCommand ConsumeBatteryCommand => new DelegateCommand(OnConsumeBattery);

public ComboPageViewModel() { }

private void OnConsumeBattery()
{
    Device.BeginInvokeOnMainThread(async () =>
    {
        for ( var i = 1; i < BatteryStatusIcons.Length; i++ )
        {
            Battery.Spans[0].Text = BatteryStatusIcons[i];
            Battery.Spans[0].ForegroundColor = BatteryColors[i];
            Battery.Spans[1].Text = BatteryStatus[i];
            await Task.Delay(2000);
        }
        Battery.Spans[0].Text = BatteryStatusIcons[0];
        Battery.Spans[0].ForegroundColor = BatteryColors[0];
        Battery.Spans[1].Text = BatteryStatus[0];
    });
}
{% endhighlight %}
<!-- markup clean_ -->

## Some useful things:

It is usually necessary to put something on another line. To do that, we can use
`Environment.NewLine`. Here is example:
{% highlight XML %}
<coolStuffWithFormattedText:CustomSpannableLabel FormattedText="{Binding NewLineText}"
                                                 HorizontalOptions="FillAndExpand"
                                                 VerticalOptions="FillAndExpand"
                                                 HorizontalTextAlignment="Center"/>
{% endhighlight %}

{% highlight C# %}    
public FormattedString NewLineText => new FormattedString
        {
            Spans =
            {
                new Span
                {
                    Text = "\uf063" + Environment.NewLine + "Stop looking at me",
                    FontFamily = "FontAwesome",
                    FontSize = 24
                }
            }
        };
{% endhighlight %}    

Another great thing, is ***non-breaking space***. For example you want to make complex
`FormattedText` where you have font icon and text together. But when it hasn't
enough horizontal space on display, text will be on another line, but icon is
still on previous line. If it is something like checkbox, you will not like it.
This is where non-breaking space do it best. It prevents this two things be
separate. It has `\u00A0` unicode.  
One more, is ***paragraph*** unicode `\u2029`. You even can flip your text with [this](http://www.sherv.net/flip.html) tool. You can use another unicodes also.
There are pretty much on the Internet.
If you know something useful of that kind of things, please share it with us.

## Conclusion:
Today we have covered not so popular, but it has to be, FormattedText property,
that with help of spans and font icons gives us flexibility and power to do various
cool things.  
 Project with ***full code*** you can find [here](https://github.com/UniorDev/CoolStuffWithFormattedText). It was good to learn it with you! :wink:       
