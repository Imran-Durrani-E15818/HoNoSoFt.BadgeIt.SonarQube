# docker build -t honosoft/badgeit .
# docker run -d -p 8080:80 --name badgeit honosoft/badgeit
# docker push honosoft/badgeit:latest
FROM microsoft/dotnet:2.1.401-sdk-alpine3.7 AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

###########################################################
# Build runtime image
FROM microsoft/dotnet:2.1.3-aspnetcore-runtime-alpine3.7

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN addgroup -S badgeit && adduser -S -G badgeit badgeit
RUN apk update; apk add curl
ENV SONAR_API_KEY ""
ENV SONAR_URI ""
ENV SHIELDS_URI ""

RUN mkdir /appsettings && chown badgeit:badgeit /appsettings
VOLUME /appsettings

WORKDIR /app
COPY --from=build-env /app/out .
RUN chown -R badgeit:badgeit /app
# Ensure we don't package any development settings.
RUN rm -f /app/appsettings.Development.json
ENTRYPOINT ["dotnet", "HoNoSoFt.BadgeIt.SonarQube.Web.dll"]

EXPOSE 80
