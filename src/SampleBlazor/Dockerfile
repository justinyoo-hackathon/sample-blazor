# syntax=docker/dockerfile:1

# Build stage
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

# Restore dependencies (cached when the project file does not change)
COPY ["SampleBlazor.csproj", "./"]
RUN dotnet restore "SampleBlazor.csproj"

# Build and publish the application
COPY . .
RUN dotnet publish "SampleBlazor.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Runtime stage
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final
WORKDIR /app

# Container Apps routes traffic to the configured target port
ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080

# Run as the non-root user provided by the base image
USER $APP_UID

COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "SampleBlazor.dll"]
