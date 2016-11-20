---
id: 597
title: 'MvvmCross &#8211; FlyoutNavigation, Hamburger Menu, Sliding Menu for Android Null Reference Exception on Fragment Shown Fix'
date: 2014-06-29T00:01:13+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=597
permalink: /archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-null-reference-exception-on-fragment-shown-fix/
categories:
  - mvvmcross
  - xamarin
---
# Summary
The architecture I used in MvvmCross for a FlyoutNavigation/Hamburger Menu/Sliding Menu requires the developer to maintain ```ViewModel``` state for all of the fragments used within the menu.  The original implementation did not provide such a service.  On certain Android devices a null reference exception would be generated when the user navigated between fragments.

The fix described here is not a perfect solution, use with caution.

# Original Articles
 * iOS and Android Implementation - [http://benjaminhysell.com/archive/2014/04/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-and-ios/](http://benjaminhysell.com/archive/2014/04/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-and-ios/)
 * Windows Phone 8 Implementation - [http://benjaminhysell.com/archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-ios-and-windows-phone/](http://benjaminhysell.com/archive/2014/06/mvvmcross-flyoutnavigation-hamburger-menu-sliding-menu-for-android-ios-and-windows-phone/)

# Source Code
[https://github.com/benhysell/V.FlyoutTest](https://github.com/benhysell/V.FlyoutTest)

# Issue
After using the slide out menu architecture in an iOS, Android, and Windows Phone app I started receiving bug reports from two different Android Samsung devices.  

```
06-09 11:36:16.740 I/MonoDroid(21351): UNHANDLED EXCEPTION: System.NullReferenceException: Object reference not set to an instance of an object
06-09 11:36:16.740 I/MonoDroid(21351): at V.JobTrak.Apps.Droid.Views.EnterTimeView.OnCreateView (Android.Views.LayoutInflater,Android.Views.ViewGroup,Android.OS.Bundle) <IL 0x000cf, 0x00588>
06-09 11:36:16.740 I/MonoDroid(21351): at Android.Support.V4.App.Fragment.n_OnCreateView_Landroid_view_LayoutInflater_Landroid_view_ViewGroup_Landroid_os_Bundle_ (intptr,intptr,intptr,intptr,intptr) <IL 0x00026, 0x0019f>
06-09 11:36:16.740 I/MonoDroid(21351): at (wrapper dynamic-method) object.d4c53d5a-8a99-43f6-bd55-88a30287aec2 (intptr,intptr,intptr,intptr,intptr) <IL 0x00023, 0x0005f>
An unhandled exception occured.

Unhandled Exception:

Java.Lang.Throwable: Loading...

06-09 11:36:20.620 D/HockeyApp(21351): Writing unhandled exception to: /data/data/com.droid.apps.jobtrak.v/files/f6e8f1e8-532a-49e9-99e2-00970568f77a.stacktrace
06-09 11:36:20.620 E/mono-rt (21351): [ERROR] FATAL UNHANDLED EXCEPTION: Java.Lang.Throwable: Exception of type 'Java.Lang.Throwable' was thrown.
06-09 11:36:20.620 E/mono-rt (21351): at Android.Runtime.JNIEnv.NewString (string) <IL 0x0004c, 0x00260>
06-09 11:36:20.620 E/mono-rt (21351): java.lang.Throwable: System.NullReferenceException: Object referenc06-09 11:36:20.620 E/mono-rt (21351): at Android.Util.Log.Info (string,string) <IL 0x0002e, 0x00127>
06-09 11:36:20.620 E/mono-rt (21351): at Android.Runtime.AndroidEnvironment.UnhandledException (System.Exception) <IL 0x00010, 0x00093>
06-09 11:36:20.620 E/mono-rt (21351): at (wrapper dynamic-method) object.da665a14-2c2b-48b2-9db0-7303fcb7fde2 (intptr,intptr) <IL 0x00029, 0x0008f>
06-09 11:36:20.620 E/mono-rt (21351): at (wrapper native-to-managed) object.da665a14-2c2b-48b2-9db0-7303fcb7fde2 (intptr,intptr) <IL 0x0001a, 0x0006b>
06-09 11:36:20.620 E/mono-rt (21351): 
06-09 11:36:20.620 E/mono-rt (21351):   --- End of managed exception stack trace ---
06-09 11:36:20.620 E/mono-rt (21351): java.lang.Throwable: System.NullReferenceException: Object reference not set to an instance of an object
06-09 11:36:20.620 E/mono-rt (21351): at V.JobTrak.Apps.Droid.Views.EnterTimeView.OnCreateView (Android.Views.LayoutInflater,Android.Views.ViewGroup,Android.OS.Bundle) <IL 0x000cf, 0x00588>
06-09 11:36:20.620 E/mono-rt (21351): at Android.Support.V4.App.Fragment.n_OnCreateView_Landroid_view_LayoutInflater_Landroid_view_ViewGroup_Landroid_os_Bundle_ (intptr,intptr,intptr,intptr,intptr)
The program 'Mono' has exited with code 0 (0x0).
```

Same crash, two different devices.  I couldn't for the life of me reproduce it on my Nexus 5 and was at a loss as to what was going on.

# Getting Help
I turned to StackOverflow, [http://stackoverflow.com/questions/24145410/mvvmcross-android-null-reference-for-viewmodel-when-reloading-fragments](http://stackoverflow.com/questions/24145410/mvvmcross-android-null-reference-for-viewmodel-when-reloading-fragments) and almost immediately I received a response from [Stuart Lodge](https://twitter.com/slodge) the creator of MvvmCross.

[From his answer on StackOverflow](http://stackoverflow.com/a/24156426/7561):

<blockquote>
From your code, it looks like you are manually setting the ViewModel's of your fragments when you first create them:

            frag.ViewModel = viewModelLocal;

[https://github.com/benhysell/V.FlyoutTest/blob/master/V.FlyoutTest.Droid/Views/HomeView.cs#L153](https://github.com/benhysell/V.FlyoutTest/blob/master/V.FlyoutTest.Droid/Views/HomeView.cs#L153)
</blockquote>

Which, sure enough, that was exactly what was going on, from the ```HomeView.cs\Show()``` in the Android project:

```
var loaderService = Mvx.Resolve<IMvxViewModelLoader>();
var viewModelLocal = loaderService.LoadViewModel(request, null /* saved state */);
frag.ViewModel = viewModelLocal;
```

Bottom line, since I've created the ```ViewModel``` in my ```HomeView``` for a ```View``` I need to make sure it gets recreated if it is ever unloaded from memory.

# Reproducing the Error with My Device
The real kicker with this issue is I could not reproduce it on my own device, or almost any other device I tested with.  The error only showed up on a Samsung S3 and S5.  I turned to the jabbr.net chat room for MvvmCross, [https://jabbr.net/#/rooms/mvvmcross](https://jabbr.net/#/rooms/mvvmcross), for advice.  It was [Stuart Lodge](https://twitter.com/slodge) who turned me onto a devious little setting in the Android Developer Options Menu called 'Don't Keep Activities'.

<a href="http://benjaminhysell.com/wp-content/uploads/2014/06/Screenshot_2014-06-28-23-09-51.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/06/Screenshot_2014-06-28-23-09-51-168x300.png" alt="Screenshot_2014-06-28-23-09-51" width="168" height="300" class="aligncenter size-medium wp-image-603" /></a>

Check that box and Android will ensure that the second you navigate away from an activity it is completely dumped from memory.  When I checked it on the N5 I could re-create the crashes I was seeing in the field!

# Fixing the Crashes

I do not know the proper, "Ivory Tower" method to fix this particular issue.  I stumbled around for a few days trying different methods to address the null reference exception, but discovered this is a non-trivial issue.  From MvvmCross' GitHub issue tracker: [Setting ViewModel property on a Fragment is not Correct](https://github.com/MvvmCross/MvvmCross/issues/636)

<blockquote>

```
var viewModelLoader = Mvx.Resolve();
fragment.ViewModel = viewModelLoader.LoadViewModel(request, null);
```

This is incorrect because Fragments are managed by a FragmentManager and each Fragments lifecycle is at the mercy of that manager and Android. At any time your Fragment may be destroyed and the FragmentManager's job is to recreate it. When it gets recreated it will no longer have the ViewModel you gave it and this will cause problems in the program. This pattern works in ideal situations but will break in many others....
</blockquote>

The issue, as of publishing this post, is still open.  

## Warning - This Fix is a Hack
Realizing the proper fix was currently beyond my capability I went for a fix that appears to work.  Since the fix has been implemented the app no longer crashes due to this bug.  Be warned, this is a hack, understand it before you use it.

## Steps Taken to Fix the Issue
1. In ```HomeViewModel``` we'll save the ```ViewModels``` for the fragments we are creating when ```OnSaveInstanceState(Bundle outState)``` is called in hopes MvvmCross will properly save the state of our ViewModels if they are properties of the ```HomeViewModel```
2. In the ```OnCreate()``` method we'll attempt to restore the ```ViewModel``` of the fragment we are about to show, and if we can't we'll create a new one.

## Modifying ```HomeViewModel```

In the ```HomeViewModel``` we'll add public properties for each of the ```ViewModels``` of our fragments.

```
public class HomeViewModel : BaseViewModel
{
    //allows us to save state for Android
    public EnterTimeViewModel EnterTimeViewModelFragment;
    public CreateNewJobViewModel CreateNewJobViewModelFragment;
```

## Creating Our Fragments
When we create our fragments we are going to add a tag to the fragment when we add it to the ```SupportFragmentManager```.  We'll use the fragment's title property as the tag, thus later on we can check the ```SupportFragmentManager``` using the title of the fragment to see if the fragment exists and if it has a valid ```ViewModel``` attached to it.

```
this.SupportFragmentManager.BeginTransaction().Replace(Resource.Id.content_frame, frag, title).Commit();
```

## Saving Fragment State
We'll iterate through all of the fragments in our ```SupportFragmentManager```, grab their ```ViewModels``` and place them in our ```HomeViewModel``` with the hopes that if the ```HomeViewModel``` is removed from memory the standard MvvmCross state saving mechanisms will also save the state of our fragment's ```ViewModels```.

```
        protected override void OnSaveInstanceState(Bundle outState)
        {
            SaveViewModelStates();
            base.OnSaveInstanceState(outState);
        }

        private void SaveViewModelStates()
        {
            //save all of the ViewModels for fragments
            var view = this.SupportFragmentManager.FindFragmentByTag("Enter Time") as EnterTimeView;
            if (null != view)
            {
                ViewModel.EnterTimeViewModelFragment = view.ViewModel as EnterTimeViewModel;
            }
            var view2 = this.SupportFragmentManager.FindFragmentByTag("Create New Job") as CreateNewJobView;
            if (null != view2)
            {
                ViewModel.CreateNewJobViewModelFragment = view2.ViewModel as CreateNewJobViewModel;
            }            
        }
```

## Showing a Fragment
In the ```OnCreate()``` method we used to punt if we had any ```savedInstanceState``` data.

```
    if (null == savedInstanceState)
    {
        this.ViewModel.SelectMenuItemCommand.Execute(this.ViewModel.MenuItems[0]);
    }
}// end function
```

Now we will attempt to restore that state.

```
    if (null == savedInstanceState)
    {
        this.ViewModel.SelectMenuItemCommand.Execute(this.ViewModel.MenuItems[0]);
    }
    else
    {
        //restore viewModels if we have them
        RestoreViewModels();
    }
} //end of function            
 
/// <summary>
/// Restore view models to fragments if we have them and the fragments were created
/// </summary>
private void RestoreViewModels()
{
    var loaderService = Mvx.Resolve<IMvxViewModelLoader>();
            
    var view = this.SupportFragmentManager.FindFragmentByTag("Enter Time") as EnterTimeView;
    if (null != view && null == view.ViewModel)
    {
        view.ViewModel = ViewModel.EnterTimeViewModelFragment ?? loaderService.LoadViewModel(new MvxViewModelRequest(typeof(EnterTimeViewModel), null, null, null), null) as EnterTimeViewModel;
    }
    var view2 = this.SupportFragmentManager.FindFragmentByTag("Create New Job") as CreateNewJobView;
    if (null != view2 && null == view2.ViewModel)
    {
        view2.ViewModel = ViewModel.CreateNewJobViewModelFragment ?? loaderService.LoadViewModel(new MvxViewModelRequest(typeof(CreateNewJobViewModel), null, null, null), null) as CreateNewJobViewModel;
    }            
}
```
We iterate through all of the fragments in the ```SupportFragmentManager``` checking to see if our fragment was created, and if so if the ```ViewModel``` is valid.  If it is null attempt to grab it from the ```HomeViewModel``` instance we saved away, if that is also null create a new ```ViewModel``` for the fragment.

# Conclusion
This solution is a hack, it is ugly, and in no means the proper way to account for this behavior on the Android platform.  Case and point, in my testing I found often times the ```HomeViewModel``` would have a valid reference to my fragment's ```ViewModels``` when I attempted to restore them in the ```RestoreViewModels()```.  I may just be fooling myself in my limited testing, and there is a good chance that step doesn't do what I'm expecting it to do.  However, the worst case in this situation is a new ```ViewModel``` is created for the fragment, ensuring we do not get a null reference exception when we attempt to load our fragment.