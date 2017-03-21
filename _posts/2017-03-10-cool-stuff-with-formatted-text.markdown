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
try
{
   Control.Typeface = Typeface.CreateFromAsset(Forms.Context.Assets, Element.FontFamily + ".ttf");
}
catch ( Java.Lang.RuntimeException exception )
{
   if ( exception.Message == "native typeface cannot be made" || exception.Message == "Font asset not found FontAwesome.ttf" )
       Control.Typeface = Typeface.CreateFromAsset(Forms.Context.Assets, Element.FontFamily + ".otf");
   else
       throw;
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

But also we can add 
