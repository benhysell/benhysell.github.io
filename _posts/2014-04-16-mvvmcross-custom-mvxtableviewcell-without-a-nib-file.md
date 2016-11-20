---
id: 554
title: 'MvvmCross &#8211; Custom MvxTableViewCell Without a NIB File'
date: 2014-04-16T16:36:49+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=554
permalink: /archive/2014/04/mvvmcross-custom-mvxtableviewcell-without-a-nib-file/
categories:
  - mvvmcross
  - xamarin
tags:
  - mvvmcross
---
# Summary
Programmatically create a custom ```MvxTableViewCell``` without using a NIB file.

# Source Code
[https://github.com/benhysell/V.MvvmCross.CustomCell](https://github.com/benhysell/V.MvvmCross.CustomCell)

# Research
* [http://stackoverflow.com/questions/17799802/creating-mvxtableviewcell-without-nib](http://stackoverflow.com/questions/17799802/creating-mvxtableviewcell-without-nib)
* [http://forums.xamarin.com/discussion/6176/why-is-contentview-null-when-using-mvvmcross-mvxtablecellview](http://forums.xamarin.com/discussion/6176/why-is-contentview-null-when-using-mvvmcross-mvxtablecellview)

# Introduction
As I venture deeper into MvvmCross based development I had a need to create a custom ```MvxTableViewCell``` without using a NIB file.  There are several examples of how one can use a NIB file to complete this task, but as a general rule I try to stay away from NIB files and choose to create all of my iOS interfaces in code.

# Problem Domain
I'm working on a application that allows an engineer to track their time spent against all the projects they are working on.  My example user interface for this demo has a calendar with a UIDatePicker allowing the engineer to select the date and then show all of the entries they have made for that date.  

<a href="http://benjaminhysell.com/wp-content/uploads/2014/04/iOS-Simulator-Screen-shot-Apr-16-2014-3.43.25-PM.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/04/iOS-Simulator-Screen-shot-Apr-16-2014-3.43.25-PM-200x300.png" alt="iOS Simulator Screen shot Apr 16, 2014, 3.43.25 PM" width="200" height="300" class="aligncenter size-medium wp-image-557" /></a>

My data source for this example is a simple class containing the job name, a job id, and the number of hours spent on that job:
```
public class EnterTime
{
    public string JobName { get; set; }
    public string JobId { get; set; }
    public decimal Hours { get; set; }        
}
```

Attempting to display all three elements in a standard ```MvxTableViewCell``` wasn't working, thus the need for a custom cell.

# View and ViewModel
```FirstView``` and ```FirstViewModel``` are standard MvvmCross ```View```s and ```ViewModel```s...no real magic needs to take place in these for us to use a custom cell.  In this example code ```FirstViewModel``` provides functionality that simulates an async server call to retrieve data for a given date provided by the user.  ```FirstViewModel``` is also setup for pull to refresh, delete, and select from the ```UITableView```.  Thanks again goes to [James Montemagno](https://twitter.com/jamesmontemagno) for two great Gists to make pull to refresh and swipe to delete happen:

* [MvxUIRefreshControl](https://gist.github.com/jamesmontemagno/6323125)
* [MvvmCross TableView Swipe to Delete](https://gist.github.com/jamesmontemagno/6985403)

# MvxDeleteTableViewSource
I used [James Montemagno](https://twitter.com/jamesmontemagno)'s [MvvmCross TableView Swipe to Delete](https://gist.github.com/jamesmontemagno/6985403) as a starting point for my TableViewSource and proceeded with a few key modifications.

## Register Our Custom Cell Class
In the constructor we need to tell the ```UITableView``` which class we will be using for our cells, this is done via ```RegisterClassForCellReuse```.

```
private IRemove viewModel;        

public MvxDeleteTableViewSource(IRemove viewModel, UITableView tableView) : base(tableView)
{
    this.viewModel = viewModel;
    tableView.RegisterClassForCellReuse(typeof(HoursEntryCell), new NSString("HoursEntryCell"));
}
```

## Derive From MvxTableViewSource
Originally ```MvxDeleteTableViewSource``` derived from ```MvxStandardTableViewSource```, this however caused issues with data binding failing to work for my custom cell.  Deriving from ```MvxTableViewSource``` and implementing ```GetOrCreateCellFor``` solved the data binding issues. 

```
protected override UITableViewCell GetOrCreateCellFor(UITableView tableView, NSIndexPath indexPath, object item)
{
    return (HoursEntryCell)tableView.DequeueReusableCell("HoursEntryCell");
}
```

# Custom MvxTableViewCell
The ```HoursEntryCell``` turned out to be straight forward...in each constructor call ```CreateLayout``` and ensure data binding is initialized. 

```
[Register("HoursEntryCell")]
public class HoursEntryCell : MvxTableViewCell
{        
    public HoursEntryCell()            
    {
        CreateLayout();
        InitializeBindings();
    }

    public HoursEntryCell(IntPtr handle) : base(handle)
    {
        CreateLayout();
        InitializeBindings();
    }

    private UILabel jobId;
    private UILabel hours;
    private UILabel jobName;
       
    private void CreateLayout()
    {
        const int offsetStart = 10;
        Accessory = UITableViewCellAccessory.DisclosureIndicator;
        jobId = new UILabel(new RectangleF(offsetStart, 0, 75, 40));
        hours = new UILabel(new RectangleF(UIScreen.MainScreen.Bounds.Right - 85, 0, 55, 40));
        hours.TextAlignment = UITextAlignment.Right;
        jobName = new UILabel(new RectangleF(jobId.Frame.Right, 0, UIScreen.MainScreen.Bounds.Width - jobId.Frame.Width - hours.Frame.Width - (3 * offsetStart), 40));
        jobName.AdjustsFontSizeToFitWidth = true;
        jobName.Lines = 0;
        jobName.Font = jobName.Font.WithSize(10);
        ContentView.AddSubviews(jobId, jobName, hours);
    }

    private void InitializeBindings()
    {
        this.DelayBind(() =>
        {
            var set = this.CreateBindingSet<HoursEntryCell, EnterTime>();
            set.Bind(jobId).To(vm => vm.JobId);
            set.Bind(hours).To(vm => vm.Hours);
            set.Bind(jobName).To(vm => vm.JobName);
            set.Apply();
        });
    }
}
```

# Conclusion
The big hurtle I had with creating a custom ```UITableViewCell``` in MvvmCross without using a NIB file was deriving from the wrong ```MvxTableViewSource```.  Once I had that sorted out everything worked as expected, and I can now create my custom cells all in code without a NIB file.