## Camera


Dynamic type is a feature that was released in iOS 7 that allowed us - as app developers - to make our apps adapt to various sized text. This allows us to have our text increase or decrease size based on user preference. Unfortunately, by increasing or decreasing your font, you'll run into some interseting layout issues. And by interesting, I mean bad. And by bad, I mean ugly. 

Hey there, this is Brian and in the screencast, we'll be jumping into Dynamic Type and explore some issues when working with Dynamic Type. 

Now before we start, I want to give a big thanks to Jerry Beers. Jerry wrote the chapter in iOS 11 by Tutorials which is the basis of this screencast. Jerry is also video instructor here at raywenderlich.com. When you have a chance, definitely give him a follow on Twitter.

When working with dynamic type there are two things you'll need to consider: image sizes and your layout. First, we'll focus on images. As your text grows or shrinks, you'll want your images to scale with it. For smaller text, you can keep the image the same size but for larger text, you can scale the image with it. There are a few ways to do this. First, we'll use our old friend, UIFontMetrics.

## Demo

Here I have my project running, and when I switch to larger font, you'll notice that the image in the app remains the same. I want to scale this image to match the user text settings.

To do this, I open the main storyboard, I select the Doctor scene, and I create an outlet for the width constraint on the profile image view. I call profileImageSizeConstraint.

Next, I open DoctorViewController.swift and I add a constant to hold the default image size to be 128 ponts.

```
private let defaultImageSize: CGFloat = 128
```

Next, I override traitColelctionDidChange. 

```
override func traitCollectionDidChange(
  _ previousTraitCollection: UITraitCollection?) {
```

First I get the preferred content size category from the traits collection. 

```
let preferredSize = traitCollection.preferredContentSizeCategory
```

If the size category has changed from the previous content size, then I know I should scale my image.


To scale my image, I do this by changing the constraint constant.

```
profileImageSizeConstraint.constant = 
```

Now to find the right scale. First I first get a default instance of UIFontMetrics. This simply references the body style. Next, I call scaledValue, passing in my defaultImageSize.

```
UIFontMetrics.default.scaledValue(for: defaultImageSize)
```

This code works great for iPhone 8 and above but for 6, 6s, 7, I need to make some additional changes. First I open the main storyboard. I change the width constraint on the profile image view to be 999 and I add a trailing constraint on the profile image view to be greater than or equal to 16 to the safe area. 

Now I build and run and you'll see that my profile image view has scaled with my text. Once I change my text, the image changes with it.

## Camera

Another way to handle image sizing is to handle your image scaling for only people have electred to use the five additional accessibilty text sizes. This means, your images will remain the same for most styles, but once an accessibilty style is selected, then then images will scale.

What's nice about this approach is that this is auto-magically handled by ios. This works via the adjusts Image Size for Accessibility Content Size Category property. By setting it true, you get this functionality without writing additional code.

## Demo

To allow iOS to scale my images, I'm going to make a few changes to my project. First, I open DoctorViewController.swift and remove the traitCollectionDidChange method. 

Next, I open my storyboard and remove the image view width constraint. Finally, I select my image view and select the adjusts image size checkbox.

Now I build and run and change the text size. First I make the text small, and you'll see that that the image doesn't change. But when I scale up the text, the image scales as well. This is all done with with  a click of a check box. No code required.

## Conclusion

Asd you can see there are two different ways to scale your images along with your text. UIFontMetrics allows you scale your font against a particuliar size while you can also set a property on the image view itself. While you can you certainly defer to have iOS manage the size, you can also UIFontMetrics to determine the sizes of other ui elements so your UI can adapt with the size of the text.

For more tutorials or screencasts on dyanmic type, keep coming back to raywenderlich.com.  Cheers!