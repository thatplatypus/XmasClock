#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM --platform=linux/amd64 mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM --platform=linux/amd64 mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Root installations
USER root
RUN apt-get update && apt-get install -y python3 libatomic1

COPY ["XmasClock/XmasClock/XmasClock.csproj", "XmasClock/XmasClock/"]
COPY ["XmasClock/XmasClock.Client/XmasClock.Client.csproj", "XmasClock/XmasClock.Client/"]
RUN dotnet workload restore "./XmasClock/XmasClock.Client/./XmasClock.Client.csproj"
RUN dotnet restore "./XmasClock/XmasClock/./XmasClock.csproj"
COPY . .
WORKDIR "/src/XmasClock/XmasClock"
RUN dotnet build "./XmasClock.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./XmasClock.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Switch back to app user
USER app

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "XmasClock.dll"]