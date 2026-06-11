# sample-blazor

A sample [Blazor Web App](https://learn.microsoft.com/aspnet/core/blazor/) built on the
**.NET 10** SDK. The app is generated with `dotnet new blazor` and ships with everything
needed to run it locally or deploy it to **Azure Container Apps** using the
[Azure Developer CLI (`azd`)](https://learn.microsoft.com/azure/developer/azure-developer-cli/).

## What this app does

This is a server-rendered Blazor web application that demonstrates the default Blazor Web App
experience with interactive server-side rendering:

- **Home** – a landing page.
- **Counter** – an interactive component that increments a counter on the server.
- **Weather** – a page that renders a simulated weather forecast.

It is intended as a minimal, working starting point for building Blazor apps and deploying
them to Azure as a container.

## Project structure

```
.
├── azure.yaml                # azd template definition
├── infra/                    # Bicep infrastructure (Azure Container Apps)
│   ├── main.bicep            # Subscription-scoped entry point (creates the resource group)
│   ├── resources.bicep       # Registry, Log Analytics, Container Apps env + app, identity
│   └── main.parameters.json  # Parameters bound to azd environment values
└── src/
    └── SampleBlazor/         # The Blazor Web App
        ├── Components/        # Razor components and pages
        ├── Dockerfile        # Container image used for deployment
        └── SampleBlazor.csproj
```

## Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0)
- [Docker](https://www.docker.com/) (only required to build the container image locally)
- [Azure Developer CLI (`azd`)](https://learn.microsoft.com/azure/developer/azure-developer-cli/install-azd) (only required to deploy to Azure)
- An Azure subscription (only required to deploy to Azure)

## Getting started locally

From the repository root:

```bash
cd src/SampleBlazor
dotnet run
```

`dotnet run` prints the local URL (for example `https://localhost:7xxx`). Open it in a browser
to view the app.

### Run as a container

To build and run the published container image exactly as it runs in Azure:

```bash
cd src/SampleBlazor
docker build -t sample-blazor .
docker run --rm -p 8080:8080 sample-blazor
```

Then browse to <http://localhost:8080>.

## Getting started on Azure

The repository includes an `azd` template that provisions the required Azure resources and
deploys the containerized app to **Azure Container Apps**.

```bash
# Sign in to Azure
azd auth login

# Provision infrastructure and deploy the app in one step
azd up
```

`azd up` will prompt for:

- an **environment name** (used to name the resource group `rg-<environment-name>`),
- an **Azure subscription**, and
- an **Azure region**.

When the command completes, `azd` prints the public URL of the deployed app
(the `WEB_BASE_URL` output).

### What gets deployed

`azd up` provisions the following resources into a new resource group:

- **Azure Container Registry** – stores the app's container image.
- **Azure Container Apps environment** – the managed hosting environment.
- **Azure Container App** – runs the Blazor app, exposed publicly on port `8080`.
- **Log Analytics workspace** – collects container logs.
- **User-assigned managed identity** – used by the Container App to pull images from the
  registry (granted the `AcrPull` role), so no registry credentials are stored.

### Common follow-up commands

```bash
azd deploy   # Rebuild and redeploy the app without re-provisioning infrastructure
azd down     # Delete all Azure resources created by this template
```

## License

This project is licensed under the terms of the [LICENSE](./LICENSE) file.