---
id: 573
title: 'MvvmCross &#8211; Keychain Plugin with Windows Phone 8 Silverlight Support'
date: 2014-06-22T14:27:14+00:00
author: benjamin hysell
layout: post
guid: http://benjaminhysell.com/?p=573
permalink: /archive/2014/06/mvvmcross-keychain-plugin-with-windows-phone-8-silverlight-support/
categories:
  - mvvmcross
  - xamarin
tags:
  - mvvmcross
---
# Summary
Unified MvvmCross Keychain Plugin for iOS, Android, and Windows Phone 8 Silverlight

# Source Code
Original - [https://github.com/wedkarz/IHS.MvvmCross.Plugins.Keychain](https://github.com/wedkarz/IHS.MvvmCross.Plugins.Keychain)
Updated with Windows Phone Support - [https://github.com/benhysell/IHS.MvvmCross.Plugins.Keychain](https://github.com/benhysell/IHS.MvvmCross.Plugins.Keychain)

# Introduction
The major app platforms, iOS, Android, and Windows Phone have mechanisms available to the app developer to store usernames and passwords.  The rub is, each implementation is a bit different, and none of the major frameworks have plugins for MvvmCross.

[Artur Rybak](https://github.com/wedkarz) went out and created a unifying plugin for MvvmCross, [IHS.MvvmCross.Plugins.Keychain](https://github.com/wedkarz/IHS.MvvmCross.Plugins.Keychain).  I have added onto the plugin to include support for Windows 8 Silverlight applications.

# Why?
Most of the time developers would never store raw usernames and passwords in their app, one strives to store a token or hash of these sensitive items.  If the device is compromised the attacker woudn't gain a user's raw username or password.

However, when connecting to web services often times an app developer will need the raw username and password, hence each of the major platforms provide a secure system to store these credentials.

# Implementation
[Artur Rybak](https://github.com/wedkarz)'s plugin, [IHS.MvvmCross.Plugins.Keychain](https://github.com/wedkarz/IHS.MvvmCross.Plugins.Keychain) covers iOS and Android with a simple interface that met all of my needs:

```
namespace IHS.MvvmCross.Plugins.Keychain
{
    public interface IKeychain
    {
        bool SetPassword(string password, string serviceName, string account);
        string GetPassword(string serviceName, string account);
        bool DeletePassword(string serviceName, string account);
        LoginDetails GetLoginDetails(string serviceName);
        bool DeleteAccount(string serviceName, string account);
    }
}
```

The implementation uses the idea of a ```serviceName``` so one can keep their usernames/passwords separate from other services on the device.  

# Usage
This is a great plugin because "it just works."  Need a user name and password from your MvvmCross application?

```
var keyChain = Mvx.Resolve<IKeychain>();
var user = keyChain.GetLoginDetails(SERVICE);

if(null != user)
    var loginResult = service.Login(user.Username, user.Password);
```

```keyChain.GetLoginDetails(SERVICE)``` will return ```null``` if a username and password cannot be found for your service.


# Issues
The only issue I had was on iOS, and this may have only been a simulator issue, but I found when I attempted to update a username and/or password for a service the old entries were never deleted.  To work around this issue I cleared out all user names and passwords for my particular service before adding one back in.

```
var keyChain = Mvx.Resolve<IKeychain>();
var user = keyChain.GetLoginDetails(SERVICE);
while (user != null) //clear out old user, if we don't it will hold onto all of the old users
{
    keyChain.DeletePassword(SERVICE, user.Password);
    keyChain.DeleteAccount(SERVICE, user.Username);
    user = keyChain.GetLoginDetails(SERVICE);
}                    
keyChain.SetPassword(Password, SERVICE, Username);
```

# Windows Phone 8 Silverlight Implementation
In iOS and Android it appears there is a single system wide service that handles storage of raw passwords and usernames, and hence the required ```serviceName``` when accessing the keychain.

In Windows Phone 8 Silverlight, this isn't the case.  The best suggestion I found for storing usernames and passwords was to place them in isolated storage.  I based my implementation on an answer found on StackOverflow, [Username and Password data Windows phone 8 app](http://stackoverflow.com/a/21007110/7561).

```
public bool SetPassword(string password, string serviceName, string account)
{
    // Convert the password to a byte[].
    var passwordByte = Encoding.UTF8.GetBytes(password);

    // Encrypt the password by using the Protect() method.
    var protectedPasswordByte = ProtectedData.Protect(passwordByte, null);

    // Store the encrypted password in isolated storage.
    WriteToFile(protectedPasswordByte, serviceName + account);

    // same steps for the username
    var usernameByte = Encoding.UTF8.GetBytes(account);
    var protectedUsernameByte = ProtectedData.Protect(usernameByte, null);
    WriteToFile(protectedUsernameByte, serviceName);

    return true;
}

/// <summary>
/// Write a string to isolated storage
/// </summary>
/// <param name="data"></param>
/// <param name="filePath"></param>
private void WriteToFile(byte[] data, string filePath)
{
    // Create a file in the application's isolated storage.
    IsolatedStorageFile file = IsolatedStorageFile.GetUserStoreForApplication();
    IsolatedStorageFileStream writestream = new IsolatedStorageFileStream(filePath, System.IO.FileMode.Create, System.IO.FileAccess.Write, file);

    // Write pinData to the file.
    Stream writer = new StreamWriter(writestream).BaseStream;
    writer.Write(data, 0, data.Length);
    writer.Close();
    writestream.Close();
}
```

Since we are storing files to isolated storage I named the password file ```serviceName + account``` and the username file just ```serviceName```.

Obtaining a username and password requires one to read the files from isolated storage:

```
public string GetPassword(string serviceName, string account)
{            
    return ReadIsolatedStorage(serviceName+account);
}

public string GetUsername(string serviceName)
{
    return ReadIsolatedStorage(serviceName);
}

/// <summary>
/// Given a filename read the item from isolated storage
/// </summary>
/// <param name="filename">defines item developer is looking for in isolated storage</param>
/// <returns>value found in isolated storage</returns>
private string ReadIsolatedStorage(string filename)
{
    using (var folder = IsolatedStorageFile.GetUserStoreForApplication())
    {
        string returnValue = null; //null if not found
        if (folder.FileExists(filename))
        {
            // Retrieve the item from isolated storage.
            byte[] protectedItemByte = this.ReadFromFile(filename);

            // Decrypt the item by using the Unprotect method.
            byte[] itemByte = ProtectedData.Unprotect(protectedItemByte, null);

            // Convert the password from byte to string and display it in the text box.
            returnValue = Encoding.UTF8.GetString(itemByte, 0, itemByte.Length);
        }
        return returnValue;
    }
}

        
/// <summary>
/// Read value from isolated storage
/// </summary>
/// <param name="filePath"></param>
/// <returns></returns>
private byte[] ReadFromFile(string filePath)
{
    // Access the file in the application's isolated storage.
    IsolatedStorageFile file = IsolatedStorageFile.GetUserStoreForApplication();
    IsolatedStorageFileStream readstream = new IsolatedStorageFileStream(filePath, System.IO.FileMode.Open, FileAccess.Read, file);

    // Read the PIN from the file.
    Stream reader = new StreamReader(readstream).BaseStream;
    byte[] pinArray = new byte[reader.Length];

    reader.Read(pinArray, 0, pinArray.Length);
    reader.Close();
    readstream.Close();

    return pinArray;
}
```

# Windows Phone 8.1
Windows Phone 8.1 supports the [Credential Locker](http://technet.microsoft.com/en-us/library/jj554668.aspx).  Once Windows Phone 8.1 is released I'll update the library accordingly.

# MvvmCross Plugin
Creating a plugin for MvvmCross was straightforward, I followed the path set by [Artur Rybak](https://github.com/wedkarz), I added a ```KeychainPluginBootstrap.cs.pp``` and modified the ```IHS.MvvmCross.Plugins.Keychain.nuspec``` so the Windows Phone implementation was included in the NuGet package.

# Using My Branch in a MvvmCross Application
[IHS.MvvmCross.Plugins.Keychain](https://github.com/wedkarz/IHS.MvvmCross.Plugins.Keychain) is published on NuGet.  As of publishing this blog post I have submitted a pull request to [Artur Rybak](https://github.com/wedkarz) to include my latest changes.  If you are impatient for an 'official release' here are the steps you'll need to take to get the plugin installed in your MvvmCross application.

1. Pull down a copy of [https://github.com/benhysell/IHS.MvvmCross.Plugins.Keychain](https://github.com/benhysell/IHS.MvvmCross.Plugins.Keychain)
2. Build
3. Drop to the command line and navigate to the ```common``` folder of ```IHS.MvvmCross.Plugins.Keychain```.
4. Package the plugin - ```nuget pack IHS.MvvmCross.Plugins.Keychain.nuspec```, one should now have a new NuGet package with the Keychain plugin for iOS, Android, and Windows Phone.
5. Open your MvvmCross project and setup Visual Studio to have a new, local NuGet repository.  ```Tools\Options\NuGetPackage Manager\Package Sources```
6. Add the directory where you just built the NuGet Packages.  I called my new local location ```Keychain```.
<a href="http://benjaminhysell.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-22-at-1.57.12-PM.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-22-at-1.57.12-PM-300x173.png" alt="Screen Shot 2014-06-22 at 1.57.12 PM" width="300" height="173" class="aligncenter size-medium wp-image-575" /></a>
7. Open your MvvmCross application, ```Manage NuGet Packages``` and select the keychain package from your new local NuGet repository.
<a href="http://benjaminhysell.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-22-at-1.58.45-PM.png"><img src="http://benjaminhysell.com/wp-content/uploads/2014/06/Screen-Shot-2014-06-22-at-1.58.45-PM-300x200.png" alt="Screen Shot 2014-06-22 at 1.58.45 PM" width="300" height="200" class="aligncenter size-medium wp-image-576" /></a>

# Conclusion
If one must store username's and passwords locally on the device ensure you are using a built in mechanism to do so, attempting to create your own password store is a difficult path I do not recommend.  [https://github.com/benhysell/IHS.MvvmCross.Plugins.Keychain](https://github.com/benhysell/IHS.MvvmCross.Plugins.Keychain) provides a plugin for MvvmCross to work with iOS, Android, and Windows Phone.