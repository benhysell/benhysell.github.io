---
id: 578
title: 'MvvmCross &#8211; FlyoutNavigation, Hamburger Menu, Sliding Menu for Android, iOS, and Windows Phone'
date: 2014-06-28T22:35:28+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=578
permalink: /archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-ios-and-windows-phone/
categories:
  - mvvmcross
  - xamarin
tags:
  - android
  - ios
  - mvvmcross
  - windows phone 8
  - xamarin
---
# Summary
Provide a unified architecture for a FlyoutNavigation/Hamburger Menu/Sliding Menu for Android, iOS, and Windows Phone 8 Silverlight using MvvmCross.

<a href="http://benjaminhysell.com/wp-content/uploads/2014/06/flyout-menus.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/06/flyout-menus-300x169.png" alt="flyout menus" width="300" height="169" class="aligncenter size-medium wp-image-588" /></a>
# Source Code
[https://github.com/benhysell/V.FlyoutTest](https://github.com/benhysell/V.FlyoutTest)

# Original Article
See - [http://benjaminhysell.com/archive/2014/04/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-and-ios/](http://benjaminhysell.com/archive/2014/04/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-and-ios/) for a walkthrough of the unified architecture for iOS and Android.  This article expands upon that architecture to include Windows Phone 8.0 Silverlight.
	
# Inspiration for Windows Phone 8.0 Silverlight Implementation
I had found a few Windows Phone slide out implementations, [http://slideview.codeplex.com/](http://slideview.codeplex.com/), but attempting to tie them in with MvvmCross appeared to be a bit of a tall order, and for a while I had given up on my dream of unifying a slide out menu architecture for all three platforms.

Then I read this great article from [Scott Hanselman](https://twitter.com/shanselman) [http://www.hanselman.com/blog/XamarinFormsWriteOnceRunEverywhereANDBeNative.aspx](http://www.hanselman.com/blog/XamarinFormsWriteOnceRunEverywhereANDBeNative.aspx) talking about the new [Xamarin.Forms](https://xamarin.com/forms)  where one could write all of their presentation code once, and have it natively drawn on each platform.  The app used for the demo was a iOS, Android, and Windows Phone app with a slide out menu!

Source - [https://github.com/jamesmontemagno/Hanselman.Forms](https://github.com/jamesmontemagno/Hanselman.Forms)

Astute readers will notice, [James Montemagno](https://twitter.com/JamesMontemagno) was the inspiration for the Android slide out menu in my original article:

 * [My iOS and Android slide out architecture](https://github.com/benhysell/V.FlyoutTest) 
 * [James' Android Slide Out article](http://motzcod.es/post/60427389481/effective-navigation-in-xamarin-android-part-1)


I took the evening and dissected the demo Xamarin.Forms app.  iOS and Android implemented the familiar 'hamburger' in the upper left hand corner, that when pressed, would reveal other screens the user could navigate to.  The Windows Phone however placed the button on the ```ApplicationBar```, and when pushed showed a whole new page.

Inspired, I went back to Visual Studio and proceeded to provide a Windows Phone implementation of my slide out menu architecture. 

# Windows Phone Silverlight Implementation
As I said, this is not the traditional slide out menu for Windows Phone.  If I must confess, I'm not a daily Windows Phone user, yet, but the days where I do use Windows Phone I find the slide out menu is not as prevalent on the platform as it is ingrained on iOS and Android.  My slide out implementation is a "For Now" implementation, if the platform idioms change where a true slide out becomes the norm I'll revisit the topic.


# Solution

<a href="http://benjaminhysell.com/wp-content/uploads/2014/06/wp8-entertime.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/06/wp8-entertime-300x273.png" alt="wp8 entertime" width="300" height="273" class="aligncenter size-medium wp-image-592" /></a>

There are two major elements we need to implement for our Windows Phone solution:
1. The ```HomeView.xaml``` to hold our application menu
2. An ```ApplicationBar``` to hold our slide out menu icon on each of our root views

## HomeView.xaml
The ```HomeView.xaml``` holds the list of other ```Views``` we could navigate to.  For this example I kept things simple, placing the items in a ```ListBox```.  Since our ```HomeViewModel``` already has 

```
private List<MenuViewModel> menuItems;
public List<MenuViewModel> MenuItems
{
    get { return this.menuItems; }
    set { this.menuItems = value; this.RaisePropertyChanged(() => this.MenuItems); }
}
```
We can bind to the ```MenuItems``` in our ```HomeView.xaml```.

```
<Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
    <StackPanel>
        <ListBox ItemsSource="{Binding MenuItems}" >
            <ListBox.ItemTemplate>
                <DataTemplate>
                    <StackPanel Margin="24">
                        <TextBlock Text="{Binding Title}" Tap="UIElement_OnTap" FontSize="50" ></TextBlock>
                    </StackPanel>
                </DataTemplate>
            </ListBox.ItemTemplate>
        </ListBox>
    </StackPanel>
</Grid>
```

### HACK WARNING 
Notice the ```Tap=UIElement_OnTap```?  In ```HomeView.xaml.cs``` It leads us to:

```
private void UIElement_OnTap(object sender, GestureEventArgs e)
{
    var selectedItem = ((HomeViewModel)ViewModel).MenuItems.FirstOrDefault(x => x.Title == ((TextBlock)sender).Text);
    if (null != selectedItem)
        ((HomeViewModel)ViewModel).SelectMenuItemCommand.Execute(selectedItem);
}
```
This allows us to figure out which cell the user pressed so we can navigate to the requested page.  There are better ways to get this done, however I'm not a Windows Phone 8/MvvmCross master so I settled on this implementation for now.

### Show the First View on Application Start
The only item left in our ```HomeView.xaml.cs``` is to navigate to the first view we would like our user to see when they launch the app.  We accomplish that in the ```OnNavigatedTo```

```
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    if (null == ViewModel)
    {
        base.OnNavigatedTo(e);

        var selectedItem = ((HomeViewModel)ViewModel).MenuItems.FirstOrDefault(x => x.Title == "Enter Time");
        if (null != selectedItem)
        ((HomeViewModel)ViewModel).SelectMenuItemCommand.Execute(selectedItem);
    }
}
```

In ```OnNavigatedTo``` I am guaranteed I'll have a valid ```HomeViewModel``` from which I can navigate to our first view, ```Enter Time```.


## Navigating Back to the Menu
From ```EnterTimeView``` we can get back to our menu two different ways:
1. Press the phone Back button
2. Press the ```Menu``` icon in the ```ApplicationBar```

### Setting Up the Phone Back Button
We actually don't have to provide any code for this functionality, this is built into Windows Phone 8.

### ```ApplicationBar``` Setup
First we'll add the code for our ```ApplicationBar``` to ```EnterTimeView.xaml```

```
<phone:PhoneApplicationPage.ApplicationBar>
    <shell:ApplicationBar IsVisible="True" IsMenuEnabled="True">
        <shell:ApplicationBarIconButton Click="ShowMenu" IconUri="/Toolkit.Content/ApplicationBar.Select.png" Text="Menu"/>           
        <shell:ApplicationBarIconButton Click="AddNewHoursEntry"  IconUri="/Assets/AppBar/add.png" Text="Add Hours"/>
        <shell:ApplicationBar.MenuItems>
            <shell:ApplicationBarMenuItem Click="ShowMenu" Text="Menu"/>                
            <shell:ApplicationBarMenuItem Click="AddNewHoursEntry" Text="Add Hours Entry"/>
        </shell:ApplicationBar.MenuItems>
    </shell:ApplicationBar>
</phone:PhoneApplicationPage.ApplicationBar>
```

Here I've added a second ```ApplicationBarIconButton``` and ```ApplicationBarMenuItem``` that would allow us to show another ```View``` when pressed to mimic the functionality in the iOS and Android implementations.  

<a href="http://benjaminhysell.com/wp-content/uploads/2014/06/wp8-entertime-app-bar.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/06/wp8-entertime-app-bar-300x273.png" alt="wp8 entertime app bar" width="300" height="273" class="aligncenter size-medium wp-image-590" /></a>

### Getting ApplicaitonBar.Select.png
ApplicationBar.Select.png is part of the [Windows Phone Toolkit](http://phone.codeplex.com/), add it to your project via NuGet if you'd like to use the same icon I'm using.  Mark the icon as ```Content``` to ensure it is include with your application.

## ```ShowMenu()```
The ```ShowMenu()``` function uses the ```NavigationService``` allowing us to navigate back to our menu in the ```HomeView```.

```
private void ShowMenu(object sender, EventArgs e)
{
    NavigationService.GoBack();
}
```

# Conclusion
The lack of slide out menus in Windows Phone 8 shouldn't stop us from creating a Windows Phone 8 app that uses ```ViewModels``` that were designed with the slide out menu architecture in mind. With the example code one can now create one ```.Core``` MvvmCross project based on slide out menus and use it in Windows Phone 8, iOS, and Android apps.

Grab the code and try it out: [https://github.com/benhysell/V.FlyoutTest](https://github.com/benhysell/V.FlyoutTest)