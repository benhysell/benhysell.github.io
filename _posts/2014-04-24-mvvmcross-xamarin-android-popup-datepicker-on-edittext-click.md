---
id: 566
title: 'MvvmCross &#8211; Xamarin.Android Popup DatePicker on EditText Click'
date: 2014-04-24T15:25:53+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=566
permalink: /archive/2014/04/mvvmcross-xamarin-android-popup-datepicker-on-edittext-click/
categories:
  - mvvmcross
  - xamarin
tags:
  - development
  - mvvmcross
---
# Goal
When the user clicks on an ```EditText``` box in a MvvmCross Xamarin.Android app show a date picker popup.

<a href="http://benjaminhysell.com/wp-content/uploads/2014/04/Screenshot_2014-04-24-15-09-55.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/04/Screenshot_2014-04-24-15-09-55-168x300.png" alt="Screenshot_2014-04-24-15-09-55" width="168" height="300" class="aligncenter size-medium wp-image-567" /></a>

# Research
* [http://blog.ostebaronen.dk/2013/02/using-dialogs-in-mono-for-android.html](http://blog.ostebaronen.dk/2013/02/using-dialogs-in-mono-for-android.html)
* [http://stackoverflow.com/questions/14933330/datepicker-how-to-popup-datepicker-when-click-on-edittext](http://stackoverflow.com/questions/14933330/datepicker-how-to-popup-datepicker-when-click-on-edittext)
* [http://docs.xamarin.com/recipes/android/other_ux/fragment/select_a_date_in_a_fragment/](http://docs.xamarin.com/recipes/android/other_ux/fragment/select_a_date_in_a_fragment/)

# Solution
All of the solutions I kept finding to this problem always had the user clicking on a button to show the DatePicker popup, and I really wanted the user to be able to show the date picker when they pressed on the ```EditText``` box, much like Android does in the default Calendar app.

From the above articles I was able to piece together a solution, the highlights:

* Instead of setting the click event on a button set it to the ```EditText``` box.

```
datePickerText = view.FindViewById<EditText>(Resource.Id.DatePickerEditText);            
datePickerText.Click += delegate
{
    var dialog = new DatePickerDialogFragment(Activity, Convert.ToDateTime(datePickerText.Text), this);
    dialog.Show(FragmentManager, "date");
};
```

* Ensure when the user presses on the ```EditText``` box that the system does not display the default keyboard by setting ```datePickerText.Focusable = false;```.

* For MvvmCross binding we only need to bind to the ```datePickerText```.

The full solution: 

```
public class EnterTimeView : MvxFragment, DatePickerDialog.IOnDateSetListener
{
    private EditText datePickerText;
    
    public EnterTimeView()
    {
        this.RetainInstance = true;
    }

    public override Android.Views.View OnCreateView(Android.Views.LayoutInflater inflater, Android.Views.ViewGroup container, Android.OS.Bundle savedInstanceState)
    {
        this.HasOptionsMenu = true;
          
        var ignored = base.OnCreateView(inflater, container, savedInstanceState);
        var view = inflater.Inflate(Resource.Layout.EnterTimeView, container, false);

        datePickerText = view.FindViewById<EditText>(Resource.Id.DatePickerEditText);
        datePickerText.Focusable = false;
        datePickerText.Click += delegate
        {
            var dialog = new DatePickerDialogFragment(Activity, Convert.ToDateTime(datePickerText.Text), this);
            dialog.Show(FragmentManager, "date");
        };

        var set = this.CreateBindingSet<EnterTimeView, EnterTimeViewModel>();
        set.Bind(datePickerText).To(vm => vm.Date);
        set.Apply();

        return view;
    }
             
    public void OnDateSet(Android.Widget.DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        datePickerText.Text = new DateTime(year, monthOfYear + 1, dayOfMonth).ToString();
    }

    private class DatePickerDialogFragment : Android.Support.V4.App.DialogFragment 
    {
        private readonly Context _context;
        private DateTime _date;
        private readonly DatePickerDialog.IOnDateSetListener _listener;

        public DatePickerDialogFragment(Context context, DateTime date, DatePickerDialog.IOnDateSetListener listener)
        {
            _context = context;
            _date = date;
            _listener = listener;
        }

        public override Dialog OnCreateDialog(Bundle savedState)
        {
            var dialog = new DatePickerDialog(_context, _listener, _date.Year, _date.Month - 1, _date.Day);
            return dialog;
        }
    }
}
```
