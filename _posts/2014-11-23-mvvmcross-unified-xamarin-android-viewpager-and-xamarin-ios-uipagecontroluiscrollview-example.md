---
id: 659
title: MvvmCross Unified Xamarin.Android ViewPager and Xamarin.iOS UIPageControl/UIScrollView Example
date: 2014-11-23T15:33:34+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=659
permalink: /archive/2014/11/mvvmcross-unified-xamarin-android-viewpager-and-xamarin-ios-uipagecontroluiscrollview-example/
categories:
  - mvvmcross
  - xamarin
  - Xamarin.Android
  - Xamarin.iOS
tags:
  - android
  - ios
  - mvvmcross
  - xamarin
---
This article provides a walkthrough for implementing a common MvvmCross.Core project for a Xamarin.Android and Xamarin.iOS applications allowing one to bind to Android's ```ViewPager``` and use a ```UIPageControl/UIScrollView``` in iOS.

### Motivation
In my app [Goal Weight](http://goallineapps.com/app/goal-weight/), I used a ```UIPageControl/UIScrollView``` combination to show the user three different graph views on the weight entry page.

<a href="http://benjaminhysell.com/wp-content/uploads/2014/11/graphs.gif"><img src="http://benjaminhysell.com/wp-content/uploads/2014/11/graphs.gif" alt="graphs" width="782" height="495" class="aligncenter size-full wp-image-660" /></a>

[Goal Weight](http://goallineapps.com/app/goal-weight/) was published before I discovered MvvmCross, so when I needed to implement a ```UIPageControl/UIScrollView``` in my latest MvvmCross application I did not have any source code to pull on.  

My goal with my new application is to get the same functionality I had in [Goal Weight](http://goallineapps.com/app/goal-weight/) using the MvvmCross framework.

### Resources
 * [https://github.com/benhysell/Cheesebaron.MvvmCross.Bindings](https://github.com/benhysell/Cheesebaron.MvvmCross.Bindings) - Source code for this article
 * [https://github.com/Cheesebaron/Cheesebaron.MvvmCross.Bindings](https://github.com/Cheesebaron/Cheesebaron.MvvmCross.Bindings) - Android implementation and inspiration for my approach.  [@Cheesebaron](https://twitter.com/Cheesebaron) is a prolific MvvmCross contributor, if you are using the MvvmCross framework I highly suggest you follow him on [Twitter](https://twitter.com/Cheesebaron), check out his [GitHub](https://github.com/Cheesebaron?tab=repositories), and subscribe to his [blog](http://blog.ostebaronen.dk/).

### Xamarin.Android Implementation
 [@Cheesebaron](https://twitter.com/Cheesebaron) did all of the leg work creating a custom binding for the Android ```ViewPager```.  My goal with the iOS implementation was to take the ```ViewModels``` he created and make sure I can use them in iOS.  I did not dive deep in to his Android binding implementation, I did test his sample application, and it worked as of the writing of this article.  

### Xamarin.iOS Implementation
Initially, I thought I was going to have to create a custom binding for the ```UIPageControl``` and ```UIScrollView``` controls, turns out I only needed to implement a custom view presenter.

#### ViewModels
We have two  ```ViewModels``` in the sample application:
 * ```SimpleListViewModel``` - holds all of the pages we are about to create
 * ```SimpleViewModel``` - the page we are showing in the ```UIPageControl/UIScrollView```

#### ```SimpleView```
 ```SimpleView``` is the page we are going to show, in this example it binds to  the ```ViewModel's``` ```Name``` property, just to show that we have successfully bound to the ```ViewModel```. 

#### ```SimpleViewPagerView```
 ```SimpleViewPagerView``` holds the ```UIPageControl/UIScrollView``` and all of the logic to create the given number of ```SimpleViews``` defined in ```SimpleListViewModel```.

All of the setup work takes place in ```ViewWillAppear```.  Below is a modified listing for brevity, see the [GitHub repo](https://github.com/benhysell/Cheesebaron.MvvmCross.Bindings) for the full code listing.
 
```
public override void ViewWillAppear(bool animated)     
{
    base.ViewDidAppear(animated);   
    pageControl.ValueChanged += HandlePageControlValueChanged;
    scrollView = new UIScrollView()
    {
        ShowsHorizontalScrollIndicator = false,
        ShowsVerticalScrollIndicator = false,
        Bounces = true,
        PagingEnabled = true,
        Frame = UIScreen.MainScreen.Bounds
    };
    scrollView.Scrolled += HandleScrollViewDecelerationEnded;
    pageControl.Frame = new RectangleF(0, scrollView.Bounds.Bottom - 10, scrollView.Bounds.Width, 10);
    View.AddSubviews(scrollView, pageControl);

    int i;
    for (i = 0; i < ViewModel.Items.Count; i++)
    {
        var pageViewController = CreatePage(ViewModel.Items[i]);
        pageViewController.View.Frame = new RectangleF(UIScreen.MainScreen.Bounds.Width * i, 0, UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height);
        scrollView.AddSubview(pageViewController.View);
    }
    scrollView.ContentSize = new SizeF(UIScreen.MainScreen.Bounds.Width * (i == 0 ? 1 : i), UIScreen.MainScreen.Bounds.Height);              
    pageControl.Pages = i; 
}
```
First we create our ```UIPageControl``` and ```UIScrollView``` and then iterate through the ```SimpleListViewModel's``` ```Items``` which holds the pages we want to create, create those pages, and lastly add the created pages to the ```UIScrollView```.  

The trickiest part of ```ViewWillAppear``` is where we set he location of our page within the ```UIScrollView```, we have to set the starting ```x``` position based on the number of the page we just created:

```pageViewController.View.Frame = new RectangleF(UIScreen.MainScreen.Bounds.Width * i, 0, UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height);```

Lastly we tell our ```UIPageControl``` how many pages we created and set the ```ContentSize``` of the ```UIScrollView``` accordingly:

 ```scrollView.ContentSize = new SizeF(UIScreen.MainScreen.Bounds.Width * (i == 0 ? 1 : i), UIScreen.MainScreen.Bounds.Height);``` 
 
We create the pages by using a MvvmCross built in function ```CreateViewControllerFor``` that takes a ```ViewModel``` and will create the ```View``` that goes along with it:

```
private UIViewController CreatePage(IMvxViewModel viewModel)
{
    var controller = new UINavigationController();           
    var screen = this.CreateViewControllerFor(viewModel) as UIViewController;              
    controller.PushViewController(screen, false);           
    return controller;
}
```

The only items left to do are to hook up the scroll events for the ```UIScrollView``` and ```UIPageControl```, see the source code on [GitHub](https://github.com/benhysell/Cheesebaron.MvvmCross.Bindings) for the full implementation.

### Summary
Using  [@Cheesebaron](https://twitter.com/Cheesebaron)'s custom Android bindings, and a custom presenter for iOS one can design a common MvvmCross core project that can feed both a Xamarin.Android and a Xamarin.iOS applications that use ```ViewPager``` and ```UIScrollView/UIPageControl``` respectively.
