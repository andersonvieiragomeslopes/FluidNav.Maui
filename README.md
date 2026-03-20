# FluidNav.Maui

`FluidNav.Maui` is an experimental plugin for fluid navigation in .NET MAUI apps, with route-based navigation and smooth transitions.

## Platform and version

This repository is configured for **.NET 10**.

## Installation (local NuGet package)

### 1) Build a local package

From the repository root:

```bash
dotnet workload restore src/FluidNav.csproj
dotnet pack src/FluidNav.csproj -c Release -p:TargetFrameworks=net10.0-android -o ./artifacts/nuget
```

The `.nupkg` file will be generated in `./artifacts/nuget`.

### 2) Register a local NuGet source

Example (adjust the path for your environment):

```bash
dotnet nuget add source /absolute/path/to/artifacts/nuget --name fluidnav-local
```

### 3) Consume it in your MAUI app

```bash
dotnet add package FluidNav.Maui --source fluidnav-local
```

## Plugin integration (step by step)

### 1) Add a host page that implements `IFluidHost`

Use a host page based on the sample implementation in:

- `samples/FluidHostPage.cs`

This host is required because `UseFluidNav<THostView>` expects `THostView : IFluidHost`.

### 2) Register FluidNav in `MauiProgram`

```csharp
using FluidNav;

builder
    .UseMauiApp<App>()
    .UseFluidNav<FluidHostPage>(routes => routes
        .AddRoute<PlaylistCollection>()
        .AddRoute<Playlist, PlaylistVM>());
```

Notes:

- The first route added becomes the default route.
- Routes are registered by view type name (for example, `PlaylistCollection`).

### 3) Set the app root page in `App.xaml.cs`

```csharp
using FluidNav;

public partial class App : Application
{
    public App(IServiceProvider services)
    {
        InitializeComponent();
        MainPage = new FluidNavigationPage(services.GetFluidHost());
    }
}
```

### 4) Navigate between pages

Use `FlowNavigation.Current` in your views/view models, for example:

```csharp
await FlowNavigation.Current.GoTo<Playlist>();
await FlowNavigation.Current.GoBack();
```

## Shell compatibility

FluidNav is designed as an alternative to MAUI Shell navigation.

- ✅ Compatible with MAUI apps that use `Application.MainPage` and `NavigationPage`.
- ⚠️ Not intended to run side-by-side with Shell navigation APIs (`Shell.Current.GoToAsync`, Shell route registration, etc.) as the primary navigation flow.
- ⚠️ In practice, choose one navigation root: **FluidNav** (`FluidNavigationPage`) or **Shell** (`AppShell`) for the main app flow.

## GitHub Actions (build + GitHub Packages publish)

Workflow:

- `.github/workflows/nuget-local.yml`

This pipeline:

1. Uses .NET 10
2. Restores workloads/dependencies
3. Builds the project
4. Creates the NuGet package in `artifacts/nuget`
5. Publishes the package to GitHub Packages (on `push` to `main`/`master` and `workflow_dispatch`)
6. Publishes the package as a workflow artifact

> Note: this workflow does not publish to nuget.org.

## Warning

Experimental project: use with caution in production.
