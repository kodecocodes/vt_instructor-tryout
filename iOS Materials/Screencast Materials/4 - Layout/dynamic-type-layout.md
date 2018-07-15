## Camera


Dynamic type is a feature that was released in iOS 7 that allowed us - as app developers - to make our apps adapt to various sized text. This allows us to have our text increase or decrease size based on user preference. Adapting your font size can mess up your layout so in this screencast, we'll not only make our text dynamic but our our user interface as well

Hey there, this is Brian and in the screencast, we'll be jumping into Dynamic Type and explore some issues when working with Dynamic Type. 

Now before we start, I want to give a big thanks to Jerry Beers. Jerry wrote the chapter in iOS 11 by Tutorials which is the basis of this screencast. Jerry is also video instructor here at raywenderlich.com. When you have a chance, definitely give him a follow on Twitter.

Now you may be thinking that to adapt our layout, there might be some special API exposed by Dynamic Type. There isn't. You do everything with auto layout. The big question - how do you know when to adjust your constraints when an accessibilty style is selected? Thankfully there's a property on preferredContentSizeCategory called isAccessibilityCategory. This property, not surprisingly, lets us know when an accessibility category has been selected by the user. 

Let's see it in action.

## Demo

Here I my project open and run I run it in the simulator with a large text size, you'll notice the layout in my table view is messed up. Putting all the text content on a single row is simply too much for the layout. Instead, I'll stack all the text elements.

First I'll open the main storyboard and I'll select one of the labels in the Appointments scene. Then I'll clear all the constraints in the cell. I'm going to setup the constraints in code.

Now I switch over to AppointmentCell.swift. Scrolling down, you'll notice that I have constraints already pre-built. I have regular constraints built and I have constraints for the large text. I alson have some common constraints this is, constraints that shared between both of them.

Let's activate the large constraints. In awakeFromNib, I'll call two methods: setupConstraints and updateConstraints:

```
override func awakeFromNib() {
  ...
  setupConstraints()
  updateConstraints()
}
```

Now to override Update Constraints to activate my constraints. First, I'll activate the common constraints followed by the large text constraints. Then I'll call the super updateConstraints method.

```
override func updateConstraints() {
  NSLayoutConstraint.activate(commonConstraints)
  
  NSLayoutConstraint.activate(largeTextConstraints)
    super.updateConstraints()
}
```

Before I run this, I'll go into the simulator and change the text size to a large size. Now, I'll build and run. The new layout looks much better. 

Unfortunately, it won't adapt to any changes. If I go back to settings and the change the text to be smaller, it will still have the same layout. I need for the layout to adapt based on whether an accessibility size has been selected.

I stop the app and in update constraints, I add an if-check to see if the text is using an accessibility style. I do this by way of the accessbility category property.

```
 if traitCollection.preferredContentSizeCategory
    .isAccessibilityCategory {
```

If an accessibility category is being used, I deactivate the regular constraints and activate the large constraints. 

```
NSLayoutConstraint.deactivate(regularConstraints)
NSLayoutConstraint.activate(largeTextConstraints)
```
Otherwise, I do the reverse.

```
} else {
  NSLayoutConstraint.deactivate(largeTextConstraints)
  NSLayoutConstraint.activate(regularConstraints)
}
```

Now I want to update the constraints when the text size has changed. To do this, I override traitCollectionDidChange.

```
override func traitCollectionDidChange(
  _ previousTraitCollection: UITraitCollection?) {
```

First I create a local variable to store whether I'm using an accessibilty category:

```
let isAccessibilityCategory = traitCollection
    .preferredContentSizeCategory.isAccessibilityCategory ```
    
Then I'll check the previous trait collection and if there's been a change, then I call setNeedsUpdateConstraints(). This will trigger another call to updateConstraints.

Now I build and run. When I look at the layout with the smaller text, it looks great. I can read everything and my text is on one row. Switching to a larger font gives me the larger text and approporiate layout to match. That's a win / win as far as I'm concerned.    

## Conclusion

When working with Dynamic Type, layout is going to be an issue. Thankfully we have a tool such as Auto Layout to help us out and with some outlets and the isAccessibilityCategory property to assist, you should have no problems making your layout adapt with your text.

Of course, if you don't know auto layout or need some pointers on how to improve your skills with it, check out our video tutorials on it. They'll be sure to point you in the right direction. 

Of course, for more screencasts and tutorials on iOS development, keep coming back to raywenderlich.com. Cheers!