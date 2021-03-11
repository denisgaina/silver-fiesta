FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
WORKDIR /app

RUN dotnet tool install --global dotnet-sonarscanner && \
    dotnet tool install --global coverlet.console

RUN dotnet sonarscanner begin \
    /n:"Org: my_project" \
    /v:"version" \
    /k:"project:my_project" \
    /d:sonar.host.url="http://localhost:9000" \
    /d:sonar.login="token" \
    /d:sonar.cs.opencover.reportsPaths=coverage.opencover.xml

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

#RUN coverlet RandomQuotes.Tests/bin/Release/netcoreapp3.1/RandomQuotes.Tests.dll \
    #--target "dotnet" --targetargs "test -c Release --no-build" --format opencover

RUN dotnet sonarscanner end /d:sonar.login="token"

# Build runtime image
# FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
# WORKDIR /app
# COPY --from=build-env /app/out .
# ENTRYPOINT ["dotnet", "RandomQuotes.dll"]
# EXPOSE 80
