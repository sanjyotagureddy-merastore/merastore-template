# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081


# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/MeraStore.Services.Sample.Api/MeraStore.Services.Sample.Api.csproj", "src/MeraStore.Services.Sample.Api/"]
COPY ["src/MeraStore.Services.Sample.Application/MeraStore.Services.Sample.Application.csproj", "src/MeraStore.Services.Sample.Application/"]
COPY ["src/MeraStore.Services.Sample.Domain/MeraStore.Services.Sample.Domain.csproj", "src/MeraStore.Services.Sample.Domain/"]
COPY ["src/MeraStore.Services.Sample.Common/MeraStore.Services.Sample.Common.csproj", "src/MeraStore.Services.Sample.Common/"]
COPY ["src/MeraStore.Services.Sample.Infrastructure/MeraStore.Services.Sample.Infrastructure.csproj", "src/MeraStore.Services.Sample.Infrastructure/"]
RUN dotnet restore "./src/MeraStore.Services.Sample.Api/MeraStore.Services.Sample.Api.csproj"
COPY . .
WORKDIR "/src/src/MeraStore.Services.Sample.Api"
RUN dotnet build "./MeraStore.Services.Sample.Api.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./MeraStore.Services.Sample.Api.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "MeraStore.Services.Sample.Api.dll"]