## Camera


Dynamic type is a feature that was released in iOS 7 that allowed us - as app developers - to make our apps adapt to various sized text. This allowed us to have our text increase or decrease size based on user preference. Unfortunately, there was a problem. Dynamic type only appears to work with Apple's font. 

It reminds me of that old Ford Model T joke that said, "you can have your car in any color so longs as it's black". Seems almost like dynamic type, you can use dymamic type so long as you use the San Francisco font. You see, other fonts don't appear to work with Dynamic type. Or do they?

Hey there, this is Brian and in the screencast, we'll be jumping into Dynamic Type by using our own custom font. That's right - we can have our cake and eat it too. 

Now before we start, I want to give a big thanks to Jerry Beers. Jerry wrote the chapter in iOS 11 by Tutorials which is the basis of this screencast. Jerry is also video instructor here at raywenderlich.com. When you have a chance, definitely give him a follow on Twitter.

Ok, onto business. 

If you have worked with Dynamic Type before, you'll know that getting your app up and running with it, involves a combination of altering your auto layout constraints and using text styles. In my case I want to use a special kind of font called American typewriter. To use this, I need for it to conform to dynamic type sizes and I can do this with a UIFontMetrics class. 

We can create a UIFontMetrics instance for a particular style, and then scale our custom font for that style. Of course, once we do this, we'll need to set adjustFontForContentSize category to true. This means, the user won't have to restart their app to get their new font size. 

Let's see this in action.

## Demo

Here I have my project open and it invovles a bunch of different scenes. Only one of them works with Dynamic Type. What I want to do is now use a custom font on all of them, and I want this font to work with dynamic type.

First I'll update all my scenes to use the American Typewriter font. To do this, I need to set my font type to custom which means I no longer have access to my styles. I get a new warning from Xcode stating that Automatically Adjust Font requires a Dynamic text style. You see, by using a custom font, Interface Builder doesn't think I'm working with dynamic type. For now, I uncheck all the automatically adjusts font which clears the warning. 

With the custom fonts setup, I need to make it adjust to the user's preferred size. To do this, I open AppointmentCell.swift.

```swift
let metrics = UIFontMetrics(forTextStyle: .body)
```

First I create a new UIFontMetrics objects for the body style. Thankfully, I can use this for multiple labels.

```
dateLabel.font = metrics.scaledFont(for: dateLabel.font)
```

Next I set each label to use a scaled font using the font that was set in the storyboard to scale.

```
dateLabel.font = metrics.scaledFont(for: dateLabel.font)
doctorNameLabel.font =
    metrics.scaledFont(for: doctorNameLabel.font)
timeLabel.font = metrics.scaledFont(for: timeLabel.font)

```

Now I want to adjusts font content size. Thankfully, I can do this in code.

```
dateLabel.adjustsFontForContentSizeCategory = true doctorNameLabel.adjustsFontForContentSizeCategory = true
timeLabel.adjustsFontForContentSizeCategory = true
```

Next, I'll set the UIFontMetrics for the DoctorViewController as well.

```
  nameLabel.font = UIFontMetrics(forTextStyle: .headline)
    .scaledFont(for: nameLabel.font)
  phoneLabel.font = UIFontMetrics(forTextStyle: .footnote)
    .scaledFont(for: phoneLabel.font)
  addressLabel.font = UIFontMetrics(forTextStyle: .footnote)
    .scaledFont(for: addressLabel.font)
  cityStateZipLabel.font = 
    UIFontMetrics(forTextStyle: .footnote)
    .scaledFont(for: cityStateZipLabel.font)
  notesLabel.font = UIFontMetrics(forTextStyle: .body)
    .scaledFont(for: notesLabel.font)

  nameLabel.adjustsFontForContentSizeCategory = true
  phoneLabel.adjustsFontForContentSizeCategory = true
  addressLabel.adjustsFontForContentSizeCategory = true
  cityStateZipLabel.adjustsFontForContentSizeCategory = true
  notesLabel.adjustsFontForContentSizeCategory = true
```  

Finally, I'll do the same for the same for MedicineViewController.

```
if let textLabel = cell.textLabel {
  textLabel.text = medicine.name
  textLabel.font = UIFontMetrics(forTextStyle: .body)
    .scaledFont(for: textLabel.font)
  textLabel.adjustsFontForContentSizeCategory = true
}

if let detailLabel = cell.detailTextLabel {
  detailLabel.text = medicine.dose
  detailLabel.font = UIFontMetrics(forTextStyle: .body)
    .scaledFont(for: detailLabel.font)
  detailLabel.adjustsFontForContentSizeCategory = true
}
``` 

Now when I build and run, I get to use my custom font only this time, it works with dynamic type.

## Conclusion

As you can see, with a little help from our friend Font Metrics, we can now get all the advantages of dynamic type only this time with our own custom font. Of course, as you've seen the in demo, working with dynamic type can also break our layout so you'll need to account for that in your own design. We'll be covering some approaches in another in the screencast. 

For more screencasts and tutorials about dynamic type and iOS development, keep coming back to raywenderlich.com. In the mean time, did you hear about the font convention in Rome? I heard that during during the opening meet and great, one font asked another, "Are you Roman too?" to which the other responded, "No, but I am Italic".

See ya next time. Cheers!