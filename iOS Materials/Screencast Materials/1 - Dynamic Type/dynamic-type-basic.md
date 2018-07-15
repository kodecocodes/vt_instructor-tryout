## Camera

Dynamic type is a feature that was released in iOS 7 that allowed us - as app developers - to make our apps adapt to various sized text. This meant users could scale down the text to provide more information onscreen. Or for users with poor vision could, well, see the apps clearly. And I must say, as a person who doesn't exactly see with 20 / 20 vision myself, I've certainly come to appreciate the feature.

Hey everyone, this is Brian and in the screencast, I'll show you how to incorporate dynamic type into your app.

Incorporating dynamic type does requires using text styles. iOS comes with a variety of different styles but prior to iOS 11, only the body style updated with dynamic type. Now, with iOS 11, all twelve styles adapt according user preference. Using one of these styles is as easy as setting your font. 

iOS 11 allows us to automatically adjust font sizes. This means that when the user changes their font while the app is running, your text will automatically update. Enabling this is just one click of a checkbox in interface builder.

Of course, when dealing with larger text, you're layout will need to adjust. In this screencast, we'll incorporate dynamic text into an app and you'll see how larger text sizes will affect existing auto layout constraints.

Before we dive into the world of Dynamnic Type, I got to give a shoutout to Jerry Beers. Jerry wrote the chapter in iOS 11 by Tutorials which is the basis of this screencast. Jerry is also video instructor here at raywenderlich.com. When you have a chance, definitely give him a follow on Twitter.

## Demo 

To get started, I'm working with an app that doesn't have dynamic type enabled. It's a medical app that lists a series of doctors. To see this with dyamic type enabled, I return to the home screen and jump over to the settings app. In the settings app, I select General, then Accessibility, and then choose Larger Text. I turn Larger Text on and then increase the scale of the text.

Switching back to the app, you'll see that the text size hasn't changed at all in accordance to user preferences. 

Thankfully, this isn't a hard thing to accomplish. In fact, Xcode provides a warning. 

First I open Xcode and then switch to Main.Storyboard. Right away, you can see a warning that says: Trailing constraints are missing on some of the views. I'll add these constraints now.

First I select the Doctor Scene and in it, I select the Name label. I give it a constraint. I chose a trailing constraint to the safe area for now. I select the constraint, and in the size inspector, I see the first item to be superview, the relation to Greater than or Equal, and the constant to be zero.

I do this for the phone, the address, the city/state/ and zip, and the notes label as well.

With my constraints in place, I need to change the label fonts. First I select the name label and for the font, I make it a type of headline. By using the built in text style, I know my font will increase in size while also being legible to end user. You can make a custom fonts work with dynamic type but that's a topic for another screencast.

I also need to make sure that automatically adjusts font is checked.

For the notes, I set it to a body style and for the others, I set them to footnote. For more information about the styles, check out apple's human interface guideline on typography.

Now when I run the app and select the doctor, my type increases but unfortuntately, I'm seeing a little collision here. To solve this, we need to start thinking with baselines.

## Camera

When creating constraints between labels in a vertical direction, you might create a standard spacing constraint between the bottom of the first label and the top of the second. 

[image 1]

This constraint will work, but there is a better way to create constraints between two text elements with standard spacing baseline-to-baseline constraints

[image 2]

The spacing that looks best between text that is very small, is not the same spacing that will look best between text that is very large. When you create a baseline-to-baseline constraint with standard spacing, the system will take text size into account when determining what that standard spacing should be.

## Demo

Back in my demo, I open the MainStoryboard. Before I can add baseline constraints between the City, State, ZipCode label and the notes label, I need to adjust my existing contraints.

First I select the Name label and in the Size Inspector, I double click on the Bottom Space to Phone Label constraint.

I change the first item to First Baseline and then change the second item to Last Baseline. Because the constant of the constraint is already set to Standard, you don't need to do anything more to get the system to position the labels using the best vertical spacing between them.

I do them for the Phone and Address labels and the address and city, state, zip code label. 

I have a somehwat complicated relationship between the Notes label, the profile image view, and the city state zip code label. I want the notes label to be below the profile image view, but if the text above it gets too tall, you also want it to always stay below the city state, zipcode label. 

To do this, I add a baseline to baseline constraint between the city, state, and zipcode label and the notes label. I set the relation to greater than or equal using the standard value.

Next, I change the constraint between the Notes top space to the profile image view, and set it to have a low priority.

Then I another constraint between the notes top space to the profile image view. I give it a relation of greater than or equal and set the constant to standard.

This will keep the notes from overlapping the image view and try to keep a standard space below it while also allowing the notes to move down (if necessary).

With that done, there's just a few touch ups left. First I increase the horizontal compression resistance priority of the profile image view to 751 to keep it from getting compressed as the text gets larger.

Next, I change the lines of the address and cty,state,zipcode label to be 0

Then I change the line break of the address and city state zip code label to word wrap.

Now I build and run and you can see how the phone number label and the address label behave differently. Large text will clip at the edge for the phohe number, but wrap to multiple lines for the address. Use whichever behavior you need in a particular situation.

## Conclusion

As you can see, incorporating dynamic type means thinking about your font choices and how you organize your constraints. This also means that you shoud think of your layout as fluid versus static. 

Needless to say, Apple has some great guidelines when working with dynamic type. For instance, they emphasize that you should use only one typeface in your app and just a few font sizes. They also reccommend that you use the built in text styles whenever possible. And of course, your custom fonts should be legible. 

For more information, definitely check out Apple's Human Interface Guideline on Typography as well watch the WWDC 2017 session, Building Apps with Dynamic Type.

Of course, you can keep coming back to raywenderlich.com  for more tutorials and  screencasts on dynamic type as well. We'll do our best to give you top quality tutorials without the eye strain. Speaking of eyes, did you hear what happened when a lab tech fell into the lens grinder? I guess he made a spectacle of himself. Cheers!






