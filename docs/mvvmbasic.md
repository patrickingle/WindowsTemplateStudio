# MVVM Basic

MVVM Basic is not a framework but provides the minimum functionality necessary to create an app using the [Model-View-ViewModel (MVVM) pattern](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel). It was created for people who can't or don't want to use a 3rd party MVVM Framework.

MVVM Basic is not intended to be a fully featured MVVM Framework and does not include some features that other frameworks do. ViewModel-first navigation, IOC, and messaging being the most obvious ones. If you want these features we recommend choosing a framework that provides or supports them.  
MVVM Basic can also serve as a basis for developers who want to create their own MVVM implementation. By providing only the most basic of extra functionality but still following common conventions it should be the easiest option if you want to modify the generated code to meet your preferred way of working.

## Core files

Projects created with MVVM Basic contain two important classes:

- `Observable`
- `RelayCommand`

`Observable` contains an implementation of the `INotifyPropertyChanged` interface and is used as a base class for all ViewModels. This makes it easy to update bound properties on the View.

`RelayCommand` contains an implementation of the `ICommand` interface to make it easy to have the View call commands on the ViewModel, rather than handle UI events directly. You can see examples of this being used in many of the pages that can be included as part of project generation including Camera, ImageGallery, Settings, and WebView.

## Navigation

MVVM Basic assumes View-based navigation. This means that a ViewModel will trigger navigation to another View. This should be done by calling the `Navigate` method on the `NavigationService` and passing the type of the page you wish to navigate to.

```csharp
NavigationService.Navigate(typeof(SettingsPage));
```

Additionally, you can pass an optional object to the page by including it as the second parameter for the method.

```csharp
NavigationService.Navigate(typeof(DetailsPage), selectedItemId);
```

When passing values this way, they can be accessed in the `OnNavigatedTo` event of the target page. Ths can be seen in the sample below which is from the ImageGallery page.

```csharp
protected override async void OnNavigatedTo(NavigationEventArgs e)
{
    base.OnNavigatedTo(e);
    await ViewModel.InitializeAsync(e.Parameter as SampleImage, e.NavigationMode);
    showFlipView.Begin();
}
```

You can learn more about Navigation within a project [here](./navigation.md).

## ViewModel persistence and lifetime

ViewModels included in the generated pages have a lifetime that is tied to the lifetime of the page that created it.

If you need to reuse a ViewModel in multiple pages, have the ViewModel remain after navigating back from the page, or have multiple instances of a page use the same ViewModel, this can be achieved by

- Making the ViewModel a singleton or other static class.
- Making the ViewModel a property of the app.

### A singleton ViewModel

The [singleton pattern](https://en.wikipedia.org/wiki/Singleton_pattern) ensures that there will only ever be a single instance of a specific type. The generated code includes a helper class for working with singletons.

If you want ViewModel to be treated as a Singleton, access it like this:

```csharp
// C#
this.DataContext = Helpers.Singleton<MainViewModel>.Instance;
```
```vb
' VB.net
this.DataContext = Helpers.Singleton(Of MainViewModel).Instance
```

### App level ViewModels

If you want to access a ViewModel from a number of different places with an app, making it a property of the `App` class is an easy way to achieve this.

```csharp
public sealed partial class App : Application
{
    ...

    // Create an instance when the app launches
    public SettingsViewModel Settings => new SettingsViewModel();

    ...
}
```

Then use it anywhere in the app like this.

```csharp
   if ((App.Current as App).Settings.IsLoggingEnabled)
   {
       ...
   }
```
