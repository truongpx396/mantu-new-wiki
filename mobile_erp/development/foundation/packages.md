---
title: Packages 
description: All the Internal packages with their release notes and the description of the functionalities they are providing
published: true
date: 2020-07-03T09:27:47.347Z
tags: packages
editor: markdown
---

# Development
## <span style='color:#e67e22;'> Displayed texts </span>
Do not hard code user displayed text, put them in resource file to support multilanguage apps. All the developer related texts (like the logs), have to be in English.

<span style='color:#e62222;'>**Bad**</span>
```csharp
public SignatureRequestsPageViewModel(ISignatureRequestsPageContract contract, IDocumentServiceContract documentService = null) : base(contract)
{
		contract.Title = "Your page";
}
```

<span style='color:#22e62a;'>**Good**</span>
```csharp
public SignatureRequestsPageViewModel(ISignatureRequestsPageContract contract, IDocumentServiceContract documentService = null) : base(contract)
{
		contract.Title = TextsResource.YourPageTitle;
}
```
## <span style='color:#e67e22;'> Duplicate code </span>
Refactore your code to remove any duplicated code.

<span style='color:#e62222;'>**Bad**</span>
```csharp
public static async Task StartBackgroundService()
{
		var connectivity = CrossConnectivity.Current;
		await Semaphore.WaitAsync();
		{
				if (connectivity.IsConnected)
				{
						await EnrollCandidatesFromDatabase();
				}
		}
		Semaphore.Release();
}

public static async Task StartForegroundService()
{
		var connectivity = CrossConnectivity.Current;
		if (connectivity.IsConnected)
		{
				await EnrollCandidatesFromDatabase();
		}
}
```

<span style='color:#22e62a;'>**Good**</span>
```csharp
public static async Task StartBackgroundService()
{
		await Semaphore.WaitAsync();
		{
			StartService(connectivity);
		}
		Semaphore.Release();
}

public static async Task StartForegroundService()
{
		StartService(connectivity);
}

private static async Task StartService(Connectivity connectivity)
{
		var connectivity = CrossConnectivity.Current;
		if (connectivity.IsConnected)
		{
			await EnrollCandidatesFromDatabase();
		}
}
```
## <span style='color:#e67e22;'> Explicit converter</span>
Use [explicit converters](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/explicit) to convert from one model to another.

<span style='color:#22e62a;'>**Good**</span>
```csharp
 public static explicit operator AnnotationMobile(Annotation annotation)
{
		return new AnnotationMobile
		{
				Category = annotation.Category,
				Page = annotation.Page,
				Image = annotation.Image
		};
}
```

You can also use [AutoMapper](https://automapper.org/) to facilitate the conversion of your models.

## <span style='color:#e67e22;'> Logs </span>
Use the [TempTraceAdministrator](https://arp.amaris.com/Wiki/Wiki/Preview/524?fileId=4676) to log user actions in the application, refer to the [Feedback package](https://arp.amaris.com/Wiki/Wiki/Preview/524?fileId=4785) for more explanation on the logging system.

<span style='color:#22e62a;'>**Good**</span>
```csharp
private async Task<bool> CheckHasPrevious(int itemId)
{
		TempTraceAdministrator.Instance.WriteAlLog(nameof(SignatureRequestsPageViewModel) + "/" + nameof(CheckHasPrevious));
		int previousIndex = 0;

		previousIndex = CurrentList.IndexOf(CurrentList.First(x => x.SignatureRequestId == itemId)) - 1;

		var newIndex = GetPreviousIndex(previousIndex);
		return newIndex != null;
}
```

Prefere to use `nameof` in order to avoid massive renaming in case on change of method or class name.

## <span style='color:#e67e22;'> Decoupled methods </span>
Avoid making a standard method with many parameters to determine its output, prefere to do different methodes more explicit that will share a common processing.

<span style='color:#e62222;'>**Bad**</span>
```csharp
public void SaveRecord<T>(T record) where T : RealmObject, IDetachable<T>
{
		Execute((realm) =>
				realm.Write(() =>
						realm.Add(record.DetachedCopy())));
}
```

<span style='color:#22e62a;'>**Good**</span>
```csharp
public void SaveTicket(TicketLocal ticket)
{
		ticket.Id = DateTime.UtcNow.Ticks;
		SaveRecord(ticket);
}

public void SaveLog(LogLocal log)
{
		log.Id = DateTime.UtcNow.Ticks;
		SaveRecord(log);
}

private void SaveRecord<T>(T record) where T : RealmObject, IDetachable<T>
{
		Execute((realm) =>
				realm.Write(() =>
						realm.Add(record.DetachedCopy())));
}
```
## <span style='color:#e67e22;'>Requesting permissions</span>
Any request to special permission has to be clearly explained to the users.

<span style='color:#e62222;'>Bad</span>

![](~/images/Bad-Permission.png)

<span style='color:#22e62a;'>Good</span>

![](~/images/Good-Permission.png)

# Formatting

## <span style='color:#e67e22;'> EditorConfig </span>
Add an **.editorconfig** file with the default .NET code style, formatting, and naming conventions to your solution to ensure code consistency.  Set it up by checking the [Microsoft documentation](https://docs.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options?view=vs-2019).
## <span style='color:#e67e22;'> Unused code </span>
Remove unused references and code from your project as well as commented code.
## <span style='color:#e67e22;'> Line break </span>
Do not use more than one line break at a time.

<span style='color:#e62222;'>**Bad**</span>
```csharp
 private async Task SignAsync(object parameter)
{
		SignCommandParameter signCommandParameter = (SignCommandParameter)parameter;
		Document.Annotation = new Annotation
		{
				Page = signCommandParameter.Page,
				Category = AnnotationCategoryMobile.Signature,
		};


		bool succeeded = await _documentService.SignDocumentAsync(Document);
		if (succeeded)
		{
				_signatureRequestsPageViewModel.RemoveElementFromList(Document.SignatureRequestId);
				_contract.NavigateToPreviousPageAsync();
		}
}
```
## <span style='color:#e67e22;'> Naming convention </span>
Look up the [Microsoft guidelines](https://docs.microsoft.com/en-us/previous-versions/dotnet/netframework-1.1/xzf533w0(v=vs.71)) for all the rules regarding naming conventions.

Keep in mind the following rules when looking for a variable or method name:
* Use explicit name
* Use proper English
* Use camel case style

Example: Name for an object containing all the data related to an event:

<span style='color:#e62222;'>**Bad**</span>
* EvntOverview
* EventDet
* Event_information

<span style='color:#22e62a;'>**Good**</span>
* EventOverview
* EventDetails
* EventInformation

## <span style='color:#e67e22;'> Classes split </span>
Do not write more than one class or interface in each file even if they are related to each other. The only exception is if you are using an `internal` class or interface.

<span style='color:#e62222;'>**Bad**</span>
```csharp
public class Event : ObservableObject
{
		public EventType Type { get; set; }
		public string Name { get; set; }
}

public enum EventType
{
		Primary,
		Secondary
}
```
## <span style='color:#e67e22;'> Code region </span>
Use regions to organize your code within a file, following this order:
* Static Public Properties
* Static Public Methods
* Static Private Properties 
* Static Private Methods 
* Constructors
* Public Properties
* Public Methods
* Protected Properties
* Protected methods
* Private Properties
* Private Methods

<span style='color:#22e62a;'>**Good**</span>
```csharp
public class Test
{
		#region Constructors
        public DocumentManager(IXFilesMobile xFilesClient = null)
        {
            // Do stuff
        }
        #endregion
       
        #region Public Methods
        public async Task<List<SignatureRequestMobile>> GetSignatureRequests(int page, bool refresh)
        {
            // Do stuff
        }
		#endregion
}
```

# Versioning

## <span style='color:#e67e22;'> TFS repository </span>
<span style='color:#FF0000;'> **All code must be under subversion!** </span>

The code of every mobile application has to be in the TFS repository of its associated Product.

![](~/images/tfs_repository.png)

Every mobile application repository has to have at least the 3 following branch, to track the code:
* `prod` with the code of the current PROD application
* `qa` with the code of the current QA application
* `inte` with the code of the current INTE application

Plus other branches for current developement.
## <span style='color:#e67e22;'>App versioning</span>
Mobile applications follow the `x.abc` versioning format where:
* `x` is increased when the application change completely
* `a` is increased when big features are added to the application, adding a great value without changing the application completely
* `b` is increased for small features changed or added
* `c` is increased for releases part of a bug fix

## <span style='color:#e67e22;'>Package versioning</span>
Packages follow the `x.y.z` versioning format where:
* `x` is a MAJOR release with backwards incompatible API changes, for example when a non-private class or method is renamed
* `y` is a MINOR release with backwards compatible API changes, for example when  a new method is added to a class
* `z` is a PATCH release with backwards compatible bug fixes, for xeample when the code is changed in a method block but the non-private method name is not changed

This format is what [Nuget expects](https://docs.microsoft.com/en-gb/nuget/concepts/package-versioning) and it is called [Semantic Versioning 2](https://semver.org/).