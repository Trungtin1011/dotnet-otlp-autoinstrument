FROM mcr.microsoft.com/dotnet/sdk:8.0-jammy

# install OpenTelemetry .NET Automatic Instrumentation
ARG OTEL_VERSION=1.7.0
ADD https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/download/v${OTEL_VERSION}/otel-dotnet-auto-install.sh otel-dotnet-auto-install.sh
RUN apt-get update && apt-get install -y unzip && \
    OTEL_DOTNET_AUTO_HOME="/otel-dotnet-auto" sh otel-dotnet-auto-install.sh

WORKDIR /app

COPY . .

RUN dotnet publish -c Release -o out

ENV SERVICE_ENDPOINT="http://127.0.0.1:5000"

CMD ["/bin/sh", "-c", "./out/Examples.Client ${SERVICE_ENDPOINT}"]