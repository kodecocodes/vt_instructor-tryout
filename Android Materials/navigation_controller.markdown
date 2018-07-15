# Screencast Metadata

## Screencast Title

Jetpack: Navigation Controller

## Screencast Description

In this video tutorial, use Android Studio 3.2 Canary to see how to get started with the new Navigation Controller, a new Architecture Component in Android Jetpack.

## Language, Editor and Platform versions used in this screencast:

* **Language:** Kotlin 1.2
* **Platform:** Android 4.4+
* **Editor**: Android Studio 3.2

[Slide 1 - Navigation Controller - drop on Jetpack]

There were **so** many announcements at the recent Google I/O 2018. One of the larger ones for Android was the release of **Android Jetpack**, a consolidation of a number of first-party Android libraries into a single package. In this screencast we'll take a look at one of the **new** components within Jetpack, the **Navigation Controller**.

[Slide 2 - Jetpack]

Jetpack is broken up into four main parts: Foundation, UI, Behavior, and Architecture. Each of the parts contains existing libraries and tools, for example, the AppCompat library and Android Architecture Components. Three parts of Jetpack contain **new** components, and the Navigation Controller is one of the more surprising and interesting ones.

[Slide 3 - Navigation Graph]

Using the Navigation Controller, you specify your app navigation flow graphically. This is reminiscent of the **Storyboard** editor that Apple includes in Xcode for iOS development. At I/O, Google also recommended a single activity app architecture. One possible navigation structure is to provide a host fragment in that single activity within which new fragments are **swapped** as your user navigates in the app. Using the Navigation Controller, you're relieved of writing the boilerplate code you **used** to have to write for Fragment transactions.

[Slide 4 - Sample Project]

In this screencast we'll build from scratch an app named **Nav of Thrones**  that uses Jetpack Navigation. You'll see how to connect differents parts of the navigation graph with **actions** and how to pass data around between parts of the graph using **generated classes****.

## Scripted Demo

You'll need Android Studio 3.2 Canary 14 or later to follow along. The Canary version can be installed alongside a stable version of Android Studio.

Choose "Start a new Android Studio project" in from the Welcome to Android Studio window.

Name the project NavOfThrones and choose an appropriate company domain and project location. Then click Next.

Set a min API level of 21, and hit Next.

Choose "Activity & Fragment+ViewModel" on the "Add an Activity to Mobile" screen and hit Next.

Accept the defaults except leave "Fragment package path" blank and hit Finish.

Then late Gradle sync and run the build.

Note that creating the new project may fail with the canary build if project directory you choose already exists.

[Maybe close and re-open project if needed.]

The project template in the canary build has some bugs for that fragment package path, so we need to do a little cleanup before we proceed. 

Build and run the template project on an Android device or emulator.

The template project contains three Kotlin files: MainActivity, MainFragment, and MainViewModel for the fragment.

The view model is not doing anything and we won't use it in this screencast.

MainActivity loads MainFragment using a fragment transaction. We'll remove that code when we switch to using the Navigation Controller.

To add Jetpack navigation to the project, first update the project build.gradle file with the navigation library version and a classpath for the navigation safeargs gradle plugin.

```groovy
ext.nav_version = '1.0.0-alpha01'

classpath "android.arch.navigation:navigation-safe-args-gradle-plugin:$nav_version"
```

The navigation library is still in alpha at this time.

Next, add the navigation dependencies into the app build.gradle file:

```groovy
  // Navigation
  implementation "android.arch.navigation:navigation-fragment:$nav_version"
  implementation "android.arch.navigation:navigation-ui:$nav_version"
```

I'm going to go ahead and update my gradle version in gradle-wrapper.properties to 4.7.

With the gradle file chanegs in place, we now sync our Gradle files.

```
distributionUrl=https\://services.gradle.org/distributions/gradle-4.7-all.zip
```

One thing we will not do in this screencast is choose the "Refactor to Android X" option, which updates the dependencies and packages to AndroidX, because there are bugs in that refactor with the Canary 14 and 15 versions of Android Studio.

*************

DON'T DO THIS nor "Refactor to Android X" due to incompatibilites with Android Studio 3.2 Canary 14. Should be fixed in Canary 15 as mentioned at I/O. gradle.properties

```
android.useAndroidX=true
android.enableJetifier=true
```

Refactor > Refactor to AndroidX...

*************

### Create the nav graph

Now we'll start building our navigation graph. Right click on the res folder and add a new Android Resource directory for Navigation.

Right-click on res/navigation and choose New > Android Resource File.

Use the filename nav_graph. The nav_graph.xml file opens up in a Design view.

### Host Fragment

Open up  `main_activity.xml` and add a host fragment for our navigation.

```xml
  <fragment
    android:id="@+id/my_nav_host_fragment"
    android:name="androidx.navigation.fragment.NavHostFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:defaultNavHost="true"
    app:navGraph="@navigation/nav_graph" />
```

Note the defaultNavHost and navGraph attributes in the fragment.

Go back to nav_graph.xml and you should see main_activity as the HOST. If not, close and reopen the project in the Canary build.

### Adding a Start Destination

Hit the New Destination button and select main_fragment.

Switch to the Text view, see that it's been added as `app:startDestination`:

```groovy
app:startDestination="@id/mainFragment"
```

Since we've added MainFragment as the startDestination in the navigation, we can now remove the fragment transaction from MainActivity onCreate():

```kotlin
    if (savedInstanceState == null) {
      supportFragmentManager.beginTransaction()
          .replace(R.id.container, MainFragment.newInstance())
          .commitNow()
    }

```

Build and run the app. You see that thanks to the navigation graph, MainFragment is still shown, despite the fact that you removed the fragment transaction.

Now we'll add some more fragments to navigate to.

First update `main_fragment.xml` to have two buttons:

```xml
  <Button
    android:id="@+id/starkButton"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/stark"
    app:layout_constraintBottom_toTopOf="@+id/targaryenButton"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

  <Button
    android:id="@+id/targaryenButton"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/targaryen"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@+id/starkButton" />
```

We have a Stark button and a Targaryen button. 

We're going to add two fragments that we'll navigate to from the two buttons in MainFragment.

Convert the hardcoded strings to be string resources.

###  Add some fragments

Now add a few strings that we'll need to strings.xml:

```xml
  <string name="aegon">Aegon</string>
  <string name="promise_me_ned">Promise me, Ned</string>
  <string name="stark">Stark</string>
  <string name="targaryen">Targaryen</string>
  <string name="jon_snow">Jon Snow</string>
  <string name="prince">The prince that was promised.</string>

```

Next we'll add layout files for the two new fragments.

First **lyanna_fragment.xml**, which contains one button with the string promise_me_ned:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent">

  <Button
    android:id="@+id/button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/promise_me_ned"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

Hit reformat code to get the proper formatting.

Now copy lyanna_fragment.xml to **rhaegar_fragment.xml** and just replace the string on the button to be aegon.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent">

  <Button
    android:id="@+id/button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/aegon"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

Now we'll add the Fragment classes to the main package.

First **LyannaFragment** with a few lifecycle overrides and one TODO.

```kotlin
class LyannaFragment : Fragment() {

  companion object {
    fun newInstance() = LyannaFragment()
  }

  override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                            savedInstanceState: Bundle?): View {
    return inflater.inflate(R.layout.lyanna_fragment, container, false)
  }

  override fun onActivityCreated(savedInstanceState: Bundle?) {
    super.onActivityCreated(savedInstanceState)

    // TODO: add click listener
  }
}
```

Make sure the Fragment import is

```kotlin
import android.support.v4.app.Fragment
```

since as announced at I/O 2018, the platform Fragment class is being deprecated .

Now copy LyannaFragment into **RhaegarFragment** and just change the layout file that is inflated to be the correct one:

```kotlin
class RhaegarFragment : Fragment() {

  companion object {
    fun newInstance() = RhaegarFragment()
  }

  override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                            savedInstanceState: Bundle?): View {
    return inflater.inflate(R.layout.rhaegar_fragment, container, false)
  }

  override fun onActivityCreated(savedInstanceState: Bundle?) {
    super.onActivityCreated(savedInstanceState)

    // TODO: add click listener
  }
}
```

Go back to nav_graph.xml and click the New Destination button.

Add LyannaFragment to the graph.

Click on the anchor on mainFragment and drag an action to lyannaFragment.

Repeat the same steps for rhaegarFragment.

Going to the text view, you see actions added to MainFragment which have destinations to id's that were added for the other two fragments.

Go to the MainFragment Kotlin code and add button click listeners in onActivityCreated. 

```kotlin
    starkButton.setOnClickListener {
      view?.let { Navigation.findNavController(it).navigate(R.id.lyannaFragment) }
    }

    targaryenButton.setOnClickListener {
      view?.let { Navigation.findNavController(it).navigate(R.id.rhaegarFragment) }
    }
```

We're using Kotlin Android Extensions to get button references. And we use the Navigation class to find the navigation controller and then navigate to the fragments by their ID. There are no fragment transations in sight.

Build and run the app.

Then navigate to the two new fragments from the main fragment buttons.

We've not setup any back navigation, but you can navigate back to MainFragment using the device back button.

To prepare for passing data between fragments, let's add one more fragment to the graph. 

Add a layout file named Jon_snow_fragment.xml with a single text view with ID whoAmI.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:tools="http://schemas.android.com/tools"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  xmlns:app="http://schemas.android.com/apk/res-auto">

  <TextView
    android:id="@+id/whoAmI"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    tools:text="Who am I?"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

And add a fragment class named JonSnowFragment with an override of onViewCreated that we'll fill in soon.

```kotlin
class JonSnowFragment : Fragment() {

  companion object {
    fun newInstance() = JonSnowFragment()
  }

  override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                            savedInstanceState: Bundle?): View {
    return inflater.inflate(R.layout.jon_snow_fragment, container, false)
  }

  override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)

    // TODO: add arguments
  }
}
```

Go to the navigation graph and add JonSnowFragment as a destination.

Then add actions from Lyanna and Rhaegar to Jon Snow.

Add a button click listener to both LyannaFragmet and RhaegarFragment in onActivityCreated to navigate to the JonSnowFragment:

```kotlin
    button.setOnClickListener {
      view?.let { Navigation.findNavController(it).navigate(R.id.jonSnowFragment) }
    }
```

Build and run the app.

Navigating to JonSnow, you see that nothing is shown yet on the end screen. Let's change that now by passing data into the fragment.

### Passing Data

First add the SafeArgs plugin to the app build.gradle file:

```groovy
apply plugin: 'androidx.navigation.safeargs'
```

Go to nav_graph.xml and select jonSnowFragment.

Tap the + next to Arguments and add an argument with name whoami, type string, and a default value of  No one.

Going to the text view, we see the argument added into jonSnowFragment.

```xml
    <argument
      android:name="whoami"
      android:defaultValue="No one"
      app:type="string" />
```

We'll use generated classes to pass the argument data. 

In LyannaFragment, update the click listener code to get the navigation action and then setup the passed data.

```kotlin
        val action = LyannaFragmentDirections.action_lyannaFragment_to_jonSnowFragment()
        action.setWhoami(getString(R.string.jon_snow))
        Navigation.findNavController(it).navigate(action)
```

The Directions generated class gives us access to the action, on which we attach the data to send when executing the action, using a setter for the argument name we created on JonSnowFragment.

Make the same updates in the click listener in RhaegarFragment, but pass in a different string to the action:

```kotlin
        val action = RhaegarFragmentDirections.action_rhaegarFragment_to_jonSnowFragment()
        action.setWhoami(getString(R.string.prince))
        Navigation.findNavController(it).navigate(action)
```

Now in JonSnowFragment, in onViewCreated, we get the arguments property and create a safeArgs value using fromBundle of the generated Args class, and then set the value on the TextView using the safeArgs data.

```kotlin
    arguments?.let {
      val safeArgs = JonSnowFragmentArgs.fromBundle(it)
      whoAmI.text = safeArgs.whoami
    }
```

Build and run the app

Navigate from Lyanna and Rhaegar to Jon Snow, and you can see the different data that was passed in the two cases.

### Where to go from here?

You now have a good introduction to the features available with the Jetpack Navigation Controller. Other than executing a navigation and some animation files, all the navigation is handled between fragments using the data included in the navigation graph. Features we'll cover in future tutorials are custom transitions, deep linking into your app using the Navigation Controller, and integrating Navigation with other Architecture Components in Jetpack. I hope you enjoyed this Jetpack Navigation screencast. Thanks for watching.

