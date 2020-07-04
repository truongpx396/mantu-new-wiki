---
title: Mobile Architecture
description:  Standard architecture in order to insure code quality and consistency across the mobile platforms
published: true
date: 2020-07-04T04:40:07.560Z
tags: architecture
editor: markdown
---

# 1. Layered Architecture
## <span style='color:#e67e22;'> Why this architecture?</span>
> This architecture is closely inspired from the [Microsoft architecture guidelines](https://docs.microsoft.com/en-us/xamarin/cross-platform/app-fundamentals/building-cross-platform-applications/) and enables to:
{.is-info}

* Maximize code sharing
* Split the responsabilty across the application to improve maintainabiltiy and reusability
* Keep logic localized
* Simplify unit tests
## <span style='color:#e67e22;'> Description </span>
### <span style='color:#424241;'> Structure </span>

> This architecture is divided into 7 main layers, each of them being in charge of a specifc scope of the application. 
{.is-info}


> Every layer can only reference itself or the layer(s) just **directly** (not more than one level) below it.
{.is-warning}


> Next to these, there is one last layer, the *Cross* layer, which does not follow the previous constraints and is accessible by any other layer. Normally you do not need it in your application but be aware that it exists.
{.is-success}



![layered-architecture.png](/images/mobile_erp/development/foundation/layered-architecture.png)

Development wise, each layer is implemented in a different project.

![layer-implementation.png](/layer-implementation.png)

### <span style='color:#424241;'> Layers </span>

#### User Interface Layer

It contains all the elements related to the graphical user interfaces of the application like the definition of its screens.

#### Application Layer

It contains all the application logic, you will find in there the access to native APIs, resources files, definition of the models to display...

#### Business Logic Layer

It contains the data logic, provides the data to the rest of the application and manages the different data sources.

#### Service Access Layer

It is used to access to remote sources (like web APIs) to retireve and send data.

#### Service Layer

It contains the API communication engine.

#### Data Access Layer

It contains the models and managers to manipulate data from the local database. This means that it contains the query to retrieve the data for example.

#### Data Layer

It contains the local database communication engine.

## <span style='color:#e67e22;'> Design patterns </span>
> The **Layered Architecture** is not opposed to design patterns.
{.is-info}


In the contrary, the main mobile design patterns, **MVC**, **MVP** and **MVVM**, can be refleted in this architecture as illustrated bellow.


![layered-architecture-correspondance-design-patterns.png](/layered-architecture-correspondance-design-patterns.png)

# 2. Shared Code Strategy
## <span style='color:#e67e22;'> Solution structure </span>
> The IT Mobile Factory is relying on the **.Net Ecosystem** with [.NET Standard](https://docs.microsoft.com/en-us/dotnet/standard/net-standard) and [Xamarin](https://docs.microsoft.com/en-us/xamarin/cross-platform/) in order to maximize the amount of shared code across different mobile platforms.
{.is-info}


The implementation of the **View** and its associated logic management domain, the **Presenter** or **ViewModel**, are directly related to the SDK of different mobile framework.

Thus, only the **Model** formed by the **BLL** and the layers below can be fully shared across mobile frameworks using **.NET Standard** projects.


![layered-architecture-projects.png](/layered-architecture-projects.png)

## <span style='color:#e67e22;'> Model implementation </span>
### <span style='color:#424241;'> BLL </span>
This layer contains classes to manage the data logic by interacting with the **SAL** and the **DAL**. 

The classes managing this layer should have the suffix *Manager* and the models the suffix *Mobile*.

### <span style='color:#424241;'> SAL </span>
This layer contains the implementation of the [Contracts](https://arp.amaris.com/Wiki/Wiki/Preview/486?fileId=3774). For this reason, projects using the **Contract V2** do not need to implement the **SAL** as it is in the API Client package.

### <span style='color:#424241;'> SL </span>
This layer is directly implemented on the **Amaris.Api.Client.Configurations** package that the API Client package is using and you don't need to implement it in your project.

### <span style='color:#424241;'> DAL </span>
This layer can be easily implemented by following the indication to [build a data manager](https://arp.amaris.com/Wiki/Wiki/Preview/524?fileId=4682).

The models in this layer should have the suffix *Local*.

### <span style='color:#424241;'> DL </span>
This layer is directly implemented on the [LocalDatabase](https://arp.amaris.com/Wiki/Wiki/Preview/524?fileId=4682) package and you don't need to implement it in your project.

## <span style='color:#e67e22;'> Real development </span>
Eventually, only 3 layers require to be implemented on every mobile application plus the **DAL** in case of local database support.

![layers-to-implement.png](/layers-to-implement.png)

# 3. Module Division
## <span style='color:#e67e22;'> Definitions </span>
### <span style='color:#424241;'> Product Interface </span>
> A **Product Interface** is a unique combination of software and hardware enabling users to access IT services.
{.is-info}


Today, there are 3 main types of interfaces in Mantu:
* Web app for computer
* Mobile app for smartphone
* Mobile app for desktop and tablet

In today's organization, there is only one interface of each type per **Product**.

### <span style='color:#424241;'> Products and Modules </span>
> **Products** are independent sets of features answering to specific business challenges. Due to their complexity, Products are sub-divided in **Modules** focusing on smaller business scopes. 
{.is-info}


The development of each Module is under the responsibility of a **SCRUM team**.

The exhaustive list of our Products and Modules is available on the [Product Catalogue](https://arp.amaris.com/HouseOfApps/Products).

### <span style='color:#424241;'> Apps and Modules </span>
> Putting both concepts together, it appears that the feature of the same mobile app can be associated to different Modules. This creates several challenges:
{.is-info}

* How to identify the **SCRUM team** to contact in case of a bug or feedback on the app?
* How to enable parallel **Module** development on the same mobile app? 

To answer to these challenges, we put in place the concepts of **App** and **Modules** in our apps to reflect the Product and Modules division of our organization.

Thus, one App can contain several independent Modules, where the Modules are embedding all the feature related to the corresponding Module of the Product.


![modules_organization.png](/modules_organization.png)

## <span style='color:#e67e22;'> Implementation </span>
> The [AmarisCoreApp and AmarisCoreModule](https://arp.amaris.com/Wiki/Wiki/Preview/524?fileId=4943) classes were created to implement this concept. They enable you to associate a **Module** to a specific assembly and **Modules** to a specific **App**.
{.is-info}


Thanks to them, the Module codes can be decoupled, set on separate projects and the data regarding the code ownership can be retrieved thanks to its assembly. The code owned by the App should only reference the main container of the mobile application.

![modules_comparison.png](/modules_comparison.png)

Combine with the **Layered Architecture**, each module is implementing its necessary layers.

![modules-full-structure.png](/modules-full-structure.png)

# 4. Low Coupling
## <span style='color:#e67e22;'> Dependency Injection </span>
> The **Dependency Injection** design pattern is used in order to improve maintainabiltiy and reusability of the code produced. For more details, refer to the [Microsoft documentation](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/dependency-injection).
{.is-info}


This pattern can be implemented with the [ServiceLocator]( https://arp.amaris.com/Wiki/Wiki/Preview/524?fileId=4943) class which is relying on the [Autofac](https://autofac.org/) technology.

The interfaces defined for the Dependency Injection pattern have to follow the naming  `I<class_name>Contract`.
## <span style='color:#e67e22;'>  Unit Testing </span>
> Unit tests enable you to easily secure the technical quality of an application and can be easily implemented on code using the **Dependency Injection** pattern. 
{.is-info}


Refer to the [Microsoft documentation](https://docs.microsoft.com/en-us/dotnet/core/testing/) for details on unit test implementation with **xUnit**.
