---
id: 682
title: MvvmCross WPF Slideout Menu Presenter
date: 2016-11-19T10:48:45+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=682
permalink: /archive/2016/11/mvvmcross-wpf-slideout-menu-presenter/
categories:
  - Uncategorized
tags:
  - mvvmcross
  - wpf
---
[https://github.com/benhysell/V.SlideoutMenu](https://github.com/benhysell/V.SlideoutMenu)

Sample MvvmCross WPF application with a slideout presenter. A slideout button is defined in the `HomeView` allowing one to switch between all three views. `MenuOne` has a button to show a full screen view, replacing the slideout view with a full screen view.


## Usage

Applicaiton is made up of four `Views`:


* Home - Main screen that holds the slideout button, slideout menu, and where menu items are shown
* MenuOne - Sample `View` that also includes a button to show a full screen `View`
* MenuTwo and MenuThree - Extra `Views` to fill out the application
* DetailFullScreen - A full screen view that replaces the `HomeView`, creating a 'modal' screen.  Button allows one to close this and return to the `HomeView`

## SlideoutPresenter
`SlideoutPresenter.cs` in `V.Slideout.Wpf\Utilitiles` manages swapping and replacing `Views` based on user input.  By decorating a `View's` `xaml.cs` class definition with a `Region` attribute will define where that `View` is shown.

i.e. From `MenuOneView.xaml.cs`

```
[Region(Region.BaseShowSlidingMenu)]
public partial class MenuOneView
{
```
Tells the `SlideoutPresenter` this `View` should show the slideout menu button and to replace the currently shown `View` in the `HomeView`.

For a full screen view `DetailFullScreenView.xaml.cs` is decorated with 

```
[Region(Region.FullScreenNavigateBackwards)]
public partial class DetailFullScreenView
{
``` 
Telling the `SlideoutPresenter` to replace the entire `HomeView` with the contents of `DetailFullScreenView`.  Once the user closes `DetailFullScreenView` the `HomeView` with the slideout button will again be shown the user.  `DetailFullScreenView` is stacked on top of `HomeView` to take over the whole screen.

## Slideout Menu
The slideout menu is in the `HomeView` grid that is hidden/shown via slideout menu button.  A list of our items to show in the menu are created in the `HomeViewModel` and bound to a `ListBox` of buttons.  Once a button is pressed the `View` is swapped out accordingly.
