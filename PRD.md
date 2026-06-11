# Product Requirements Document (PRD): sample-blazor

## 1. Overview

**sample-blazor** is a reference Blazor Web App built on the .NET 10 SDK that demonstrates
how to build a server-rendered web application and deploy it to Azure as a container using the
Azure Developer CLI (`azd`). It serves as a ready-to-use starting point ("getting started"
template) for developers who want a minimal, working, deployable Blazor application.

## 2. Goals

- Provide a working Blazor Web App that builds and runs out of the box on the .NET 10 SDK.
- Provide a repeatable, single-command path to deploy the app to Azure (`azd up`).
- Provide clear documentation for running the app locally and on Azure.
- Keep the infrastructure minimal, secure, and easy to understand.

## 3. Non-goals

- This is not a production-grade application; it contains no authentication, persistent
  storage, or business logic beyond the default Blazor template pages.
- It does not provide a custom domain, CDN, or advanced scaling configuration.
- It does not target SDK versions other than .NET 10.

## 4. Target users

- Developers learning Blazor and/or the Azure Developer CLI.
- Teams who need a baseline template to bootstrap new Blazor projects with Azure deployment.

## 5. Features

| # | Feature | Description |
|---|---------|-------------|
| F1 | Blazor Web App | Server-rendered Blazor app with Home, Counter, and Weather pages. |
| F2 | Interactive server rendering | Components run interactively via the server render mode. |
| F3 | Containerization | A `Dockerfile` produces a runnable container image listening on port 8080. |
| F4 | azd template | `azure.yaml` plus Bicep files provision and deploy the app to Azure Container Apps. |
| F5 | Documentation | `README.md` explains what the app does and how to run it locally and on Azure. |

## 6. Architecture

- **Application:** ASP.NET Core Blazor Web App (`Microsoft.NET.Sdk.Web`), `net10.0`.
- **Container:** Multi-stage Docker build using the official .NET 10 SDK and ASP.NET runtime
  images; the runtime container runs as a non-root user and exposes port 8080.
- **Azure hosting:** Azure Container Apps.
- **Supporting Azure resources:** Azure Container Registry, Log Analytics workspace, and a
  user-assigned managed identity (granted `AcrPull`) for credential-free image pulls.

## 7. Acceptance criteria

The product is considered complete when **all** of the following are satisfied:

### Application
- [x] **AC1** – The app is created with `dotnet new blazor` and targets `net10.0`.
- [x] **AC2** – `dotnet build -c Release` succeeds with no errors.
- [x] **AC3** – Running the app locally serves the Home page over HTTP 200, and the
  Counter and Weather pages are reachable.

### Containerization
- [x] **AC4** – A `Dockerfile` exists for the app and `docker build` completes successfully.
- [x] **AC5** – The resulting container starts and serves the app on port 8080 (HTTP 200).
- [x] **AC6** – The container runs as a non-root user.

### Azure deployment (`azd`)
- [x] **AC7** – An `azure.yaml` defines a `web` service mapped to the `src/SampleBlazor`
  project with `host: containerapp`.
- [x] **AC8** – Bicep files under `infra/` compile without errors or linter warnings and
  define a resource group, container registry, Log Analytics workspace, Container Apps
  environment, and the Container App.
- [x] **AC9** – The Container App uses a user-assigned managed identity with the `AcrPull`
  role to pull images (no admin credentials stored).
- [x] **AC10** – The deployment exposes the app publicly and outputs its base URL
  (`WEB_BASE_URL`).

### Documentation
- [x] **AC11** – `README.md` explains what the app does.
- [x] **AC12** – `README.md` documents getting started both locally and on Azure.
- [x] **AC13** – This `PRD.md` describes the app as a product and lists acceptance criteria.

## 8. Future considerations

- Add authentication (for example, Microsoft Entra ID).
- Add a CI/CD pipeline (for example, GitHub Actions via `azd pipeline config`).
- Add automated tests for the application components.
