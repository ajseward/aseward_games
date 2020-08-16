FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
WORKDIR /src
COPY aseward_games.csproj .
RUN dotnet restore "aseward_games.csproj"
COPY . .
RUN dotnet build "aseward_games.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "aseward_games.csproj" -c Release -o /app/publish

FROM nginx:latest AS final
WORKDIR /usr/share/nginx/html
COPY --from=publish /app/publish/wwwroot .
COPY nginx.conf /etc/nginx/nginx.conf