# FluidNav.Maui

`FluidNav.Maui` é um plugin experimental para navegação fluida em aplicações .NET MAUI, como alternativa ao `Shell`, com navegação por rotas e transições suaves entre views.

## Plataforma e versão

Este repositório está configurado para **.NET 10**.

## Instalação (pacote NuGet local)

### 1) Gerar pacote localmente

Na raiz do repositório:

```bash
dotnet workload restore src/FluidNav.csproj
dotnet pack src/FluidNav.csproj -c Release -p:TargetFrameworks=net10.0-android -o ./artifacts/nuget
```

O pacote `.nupkg` será gerado em `./artifacts/nuget`.

### 2) Registrar uma fonte NuGet local

Exemplo (ajuste o caminho para o seu ambiente):

```bash
dotnet nuget add source /caminho/absoluto/para/artifacts/nuget --name fluidnav-local
```

### 3) Consumir no projeto MAUI

```bash
dotnet add package FluidNav.Maui --source fluidnav-local
```

## Uso básico

### 1) Registrar o FluidNav no `MauiProgram`

```csharp
using FluidNav;

builder
    .UseMauiApp<App>()
    .UseFluidNav<FluidHostPage>(routes => routes
        .AddRoute<PlaylistCollection>()
        .AddRoute<Playlist, PlaylistVM>());
```

### 2) Definir a página principal no `App`

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

## GitHub Actions (build + empacotamento local)

Foi adicionada a workflow:

- `.github/workflows/nuget-local.yml`

Essa pipeline:

1. Usa .NET 10
2. Restaura workloads/dependências
3. Compila o projeto
4. Gera o pacote NuGet em `artifacts/nuget`
5. Publica o pacote como **artifact** da execução

> Observação: não há publicação em nuget.org. O pacote fica disponível apenas localmente (ou como artifact para download).

## Aviso

Projeto experimental: use com cautela em produção.
