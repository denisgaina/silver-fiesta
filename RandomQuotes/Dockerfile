FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
WORKDIR /app
ENV PATH="$PATH:/root/.dotnet/tools"

RUN apt-get update && apt-get install -y openjdk-11-jdk && \
    dotnet tool install --global dotnet-sonarscanner && \
    dotnet tool install --global coverlet.console --version 1.7.1

RUN dotnet sonarscanner begin \
    /n:"Org: my_project" \
    /v:"version_id" \
    /k:"project:my_project" \
    /d:sonar.host.url="http://127.0.0.1:9000" \
    /d:sonar.login="token" \
    /d:sonar.cs.opencover.reportsPaths=coverage.opencover.xml

# Copy csproj and restore as distinct layers
#COPY RandomQuotes/*.csproj ./
#RUN dotnet restore

# Copy everything else and build
COPY . /app
RUN dotnet publish -c Release -o out RandomQuotes/RandomQuotes.csproj

RUN coverlet RandomQuotes.Tests/bin/Release/netcoreapp3.1/RandomQuotes.Tests.dll \
    --target "dotnet" --targetargs "test -c Release --no-build" --format opencover

RUN dotnet sonarscanner end /d:sonar.login="token"

# Build runtime image
# FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
# WORKDIR /app
# COPY --from=build-env /app/out .
# ENTRYPOINT ["dotnet", "RandomQuotes.dll"]
# EXPOSE 80
