---
id: 522
title: 'MvvmCross &#8211; FlyoutNavigation, Hamburger Menu, Sliding Menu for Android and iOS'
date: 2014-04-05T21:56:30+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=522
permalink: /archive/2014/04/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-and-ios/
categories:
  - mvvmcross
  - xamarin
tags:
  - android
  - ios
  - mvvmcross
  - xamarin
---
# Summary
Provide a unified architecture for a FlyoutNavigation/Hamburger Menu/Sliding Menu across Android and iOS using MvvmCross.

# Updates
* Fix for Android Fragment Null Reference Exception, [http://benjaminhysell.com/archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-null-reference-exception-on-fragment-shown-fix/](http://benjaminhysell.com/archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-null-reference-exception-on-fragment-shown-fix/) 
* Windows Phone 8 Implementation, [http://benjaminhysell.com/archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-ios-and-windows-phone/](http://benjaminhysell.com/archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-ios-and-windows-phone/)

# Source Code
[https://github.com/benhysell/V.FlyoutTest](https://github.com/benhysell/V.FlyoutTest)

# Introduction
FlyoutNavigation, Hamburger Menus, and Sliding Menus (from here on out I'll refer to this type of control as a slide out menu) all describe a popular method on mobile devices of navigating application windows via a menu that slides/flies out onto the screen providing a user multiple choices on which screen they want to navigate to next.  

In my own app [Goal Weight](http://goallineapps.com/app/goal-weight/) I used the popular Xamarin component [FlyoutNavigation](https://github.com/Clancey/FlyoutNavigation) to show/hide access to settings, set goals, and view weight history.

<a href="http://benjaminhysell.com/wp-content/uploads/2014/04/Menu.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/04/Menu-169x300.png" alt="Menu" width="169" height="300" class="aligncenter size-medium wp-image-526" /></a>

For my latest project I’ve started development of a time tracking application that will run on Android and iOS. Thus, I found myself needing a cross platform slide out menu.  

# Existing Work
After Googling and researching StackOverflow I had a couple of contenders for an out of the box solution:

* [http://motzcod.es/post/60427389481/effective-navigation-in-xamarin-android-part-1](http://motzcod.es/post/60427389481/effective-navigation-in-xamarin-android-part-1)

* [http://slodge.blogspot.com/2013/07/awesome-sliding-menus-from-big-frank.html](http://slodge.blogspot.com/2013/07/awesome-sliding-menus-from-big-frank.html)

The only rub was neither one of these methodologies shared a common MvvmCross ```.Core``` project, and I didn’t want to support multiple/different ```.Core``` projects for different platforms.  I also had experience with the FlyoutNavigation component and wanted to use it for the iOS solution.  Thus I set out to create a unified architecture.

# Source Code Walkthrough
The source code up on GitHub is used to demo this unified architecture...think of it as a road map on how to implement a cross platform MvvmCross slide out menu. 

The demo application has four ViewModels:

* ```HomeViewModel``` – Holds all of the ViewModels that will appear in the slide out menu
* ```EnterTimeViewModel``` – ViewModel accessible from the slide out menu.  In the demo this view is blank.
* ```CreateNewJobViewModel``` – A second ViewModel accessible from the slide out menu.  Also blank for this demo.
* ```AddNewHoursEntryViewModel``` – A ViewModel that is created from the ```EnterTimeViewModel``` when the user presses a button on the upper right hand corner of the navigation bar.


# Android Architecture
##### Background - [http://motzcod.es/post/60427389481/effective-navigation-in-xamarin-android-part-1](http://motzcod.es/post/60427389481/effective-navigation-in-xamarin-android-part-1)

##### Source -  [https://github.com/jamesmontemagno/Xam.NavDrawer](https://github.com/jamesmontemagno/Xam.NavDrawer)

Android has a slide out menu built into later versions of Android called a Navigation Drawer.  James Montemagno does an amazing job implementing the Navigation Drawer on Android in his blog post, so I decided to use his code as a starting point/inspiration for my ```.Core``` and ```.Android``` projects.

For a full detailed breakdown of the Android architecture read James' blog.  The short version is the ```HomeViewModel``` holds the slide out menu items and ```EnterTimeView``` and ```CreateNewJobView``` are fragments that are swapped in and out of the View as commanded by the slide out menu.

I tore through James' GitHub code and re-implemented it in my example application to ensure I understood it well enough to keep moving forward.  I liked the idea of having one ```ViewModel``` to hold all of the menu data and carried this idea over to the iOS application.

# iOS Architecture
#### Warning - Although the iOS architecture works, it doesn’t “feel correct”, i.e. it has a little code smell than I would not normally feel comfortable living with.  However I'm happy enough with the solution for now.

With the iOS architecture the idea is simple, construct a ```FlyoutNavigationController``` and build up the Views that will populate it in the ```HomeView```.   

First build the ```FlyoutNavigationController``` and add it to the ```View```.

```
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    NavigationController.NavigationBarHidden = true;
    Title = "Home";
    this.View = new UIView { BackgroundColor = UIColor.White };

    navigation = new FlyoutNavigationController();

    View.AddSubview(navigation.View);
    this.AddChildViewController(navigation);
```

One key point here is ```NavigationController.NavigationBarHidden = true;```, for Views that will be managed from the ```FlyoutNavigationController``` we want to use the ```FlyoutNavigationController```'s navigation bar and not the one supplied by MvvmCross.

Next, we'll build up the ViewModels and menu elements for the ```FlyoutNavigationController```.  Data for the ViewModels and their names are held in the ```HomeViewModel```

```
//names of the views shown in the flyout
var flyoutMenuElements = new Section();
//views that will be shown when a menu item is selected
var flyoutViewControllers = new List<UIViewController>();
var homeViewModel = ViewModel as HomeViewModel;
if (homeViewModel != null)
{
    //create the ViewModels
    foreach (var viewModel in homeViewModel.MenuItems)
    {
        var viewModelRequest = new MvxViewModelRequest
        {
            ViewModelType = viewModel.ViewModelType
        };

        flyoutViewControllers.Add(CreateMenuItemController(viewModelRequest));
        flyoutMenuElements.Add(new StringElement(viewModel.Title));
    }
    navigation.ViewControllers = flyoutViewControllers.ToArray();

    //add the menu elements
    var rootElement = new RootElement("")
    {
        flyoutMenuElements
    };
    navigation.NavigationRoot = rootElement;
}
```

Creating the ```UIViewController```s in ```CreateMenuItemController```

```
private UIViewController CreateMenuItemController(MvxViewModelRequest viewModelRequest)
{
    var controller = new UINavigationController();
    var screen = this.CreateViewControllerFor(viewModelRequest) as UIViewController;
    controller.PushViewController(screen, false);
    return controller;
}
```

Lastly, we need to listen to two messages that will be emitted by all ```View```s that the ```FlyoutNavigationController``` will show.  One message to toggle the ```FlyoutNavigationController```'s menu, and another to show and hide the MvvmCross navigation bar.

```
var messenger = Mvx.Resolve<IMvxMessenger>();
navigationMenuToggleToken = messenger.SubscribeOnMainThread<ToggleFlyoutMenuMessage>(message => navigation.ToggleMenu());
navigationBarHiddenToken = messenger.SubscribeOnMainThread<NavigationBarHiddenMessage>(message => NavigationController.NavigationBarHidden = message.NavigationBarHidden);
```

One last item needs to be addressed in our ```HomeView```, setting the size of the Views that will be shown in the ```FlyoutNavigationController```.

```
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);
    navigation.View.Frame = UIScreen.MainScreen.Bounds;
    navigation.View.Bounds = UIScreen.MainScreen.Bounds;
}
```

If we fail to set the ```FlyoutNavigationController``` ```View.Frame``` and ```View.Bounds``` the ```FlyoutNavigationController``` will draw all of our views at 2x the ```Frame``` size than they should be.  

Our ```HomeView``` is now complete, let's take a look at ```EnterTimeView```.

### EnterTimeView
The ```EnterTimeView``` is going to show the slide out menu button that when toggled will show the slide out menu, plus it will show another view, the ```AddHoursEntryView``` with a button on the upper right of the navigation bar.  We'll add two buttons to the navigation bar with delegates to send message back to the ```HomeView``` to perform these actions.

```
public override void ViewDidLoad()
{
    View = new UIView { BackgroundColor = UIColor.Blue };

    base.ViewDidLoad();
    Title = "Enter Time";
    NavigationItem.LeftBarButtonItem = new UIBarButtonItem(UIBarButtonSystemItem.Pause,
                                      (delegate
                                      {
                                       //message to show the menu
                                       var messenger = Mvx.Resolve<IMvxMessenger>();
                                       messenger.Publish(new ToggleFlyoutMenuMessage(this));
                                       }));
    NavigationItem.RightBarButtonItem = new UIBarButtonItem(UIBarButtonSystemItem.Add,
                                       (delegate
                                       {
                                       //hide MvvmCross navigation bar and show next view
                                       var messenger = Mvx.Resolve<IMvxMessenger>();
                                       messenger.Publish(new NavigationBarHiddenMessage(this, false));
                                       var viewmodel = ViewModel as EnterTimeViewModel;
                                       if (viewmodel != null) viewmodel.ShowFirstView();
                                       }));
}
```

### AddHoursEntryView
Since we are showing the navigation bar for MvvmCross when we show ```AddHoursEntryView``` we need to send a message back to the ```HomeView``` to hide it again once ```AddHoursEntryView``` is dismissed by the user via the back button.

```
public override void ViewWillDisappear(bool animated)
{
    if (!NavigationController.ViewControllers.Contains(this))
    {
        // Back button was pressed.  We know this is true because self is no longer
        // in the navigation stack, hide MvvmCross's navigation menu
        var messenger = Mvx.Resolve<IMvxMessenger>();
        messenger.Publish(new NavigationBarHiddenMessage(this, true)); 
    }
    base.ViewWillDisappear(animated);
}
```
With this last step we have a working slide out menu in iOS that shares a common ```.Core``` solution with the Android project.

# Conclusion
I have the warning about the iOS code smell because of the extra steps we need to take in the Views to toggle the menu and show/hide the MvvmCross navigation bar.  I can't place my finger on the exact code smell, the solution works...but to me just doesn't feel "correct" in an Ivory Tower sort of way. 

That being said, I believe the extra messaging is a small price to pay to use the ```FlyoutNavigation``` component without modification, and to have iOS and Android share a common ```.Core``` project.

Go grab the code and try it out, [https://github.com/benhysell/V.FlyoutTest](https://github.com/benhysell/V.FlyoutTest)