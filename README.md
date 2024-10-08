# dotnet-otlp-autoinstrument

**Repo Owner**: Tin Trung Ngo


## Before you begin

This repository contains demo image for `.NET` OpenTelemetry Auto-Instrumentation.

The image is meant for demonstrating how to implement auto-instrumentation in a non-Kubernetes environment. For Kubernetes, use [OpenTelemetry Operator](https://github.com/open-telemetry/opentelemetry-operator) instead.

For Amazon ECS, Normally it is recommended to use AWS Distro for OpenTelemetry (ADOT) collector for container instrumentation. The images in this repository use the original open-source OpenTelemetry Collector instead of ADOT collector.


<br>

## Deploy .NET auto-instrumentation application

The procedures of deploying sample .NET application with auto-instrumentation is mostly identical with that of Python:
1. Build the images `service`, `mssql`, and `client` located in [dotnet-autoinstrument](./dotnet-autoinstrument) folder.
2. Upload these images to somewhere that Amazon ECS can connect to and pull these images.
3. Create ECS services for the .NET application. You can define 1 service contains all 3 containers or 3 services hosting 3 different containers.

**Note**: Each service that needed to be auto-instrumented must also has some environment variables defined as well:
- `OTEL_SERVICE_NAME`: The name of the service running in the container. If not define, the service name collected in the OpenTelemetry Collector will be `unknown_service`.
- `OTEL_RESOURCE_ATTRIBUTES`: Additional attributes to include in the service data when sending to the OpenTelemetry Collector.
- `OTEL_EXPORTER_OTLP_ENDPOINT`: Endpoint of the OpenTelemetry Collector.
- `OTEL_EXPORTER_OTLP_PROTOCOL`: Protocol used, default to `http/protobuf`
- `OTEL_TRACES_EXPORTER`: The exporting method for traces, should be `otlp` because we are sending data to OpenTelemetry Collector using OpenTelemetry Protocol (otlp)
- `OTEL_METRICS_EXPORTER`: The exporting method for metrics, should be `otlp`, change value to `none` to disable.
- `OTEL_LOGS_EXPORTER`: The exporting method for logs, should be `otlp`, change value to `none` to disable.


In additional to those environment variables, 2 .NET container `service` and `client` **MUST** have the below environment variables defined as well:
1. `CORECLR_ENABLE_PROFILING`=1
2. `CORECLR_PROFILER`="{918728DD-259F-4A6A-AC2B-B85E1B658318}"
3. `CORECLR_PROFILER_PATH`="/otel-dotnet-auto/linux-x64/OpenTelemetry.AutoInstrumentation.Native.so"
4. `DOTNET_ADDITIONAL_DEPS`="/otel-dotnet-auto/AdditionalDeps"
5. `DOTNET_SHARED_STORE`="/otel-dotnet-auto/store"
6. `DOTNET_STARTUP_HOOKS`="/otel-dotnet-auto/net/OpenTelemetry.AutoInstrumentation.StartupHook.dll"
7. `OTEL_DOTNET_AUTO_HOME`="/otel-dotnet-auto"

For detailed example of environment variables, go through the [README](./dotnet-autoinstrument/README.md) file to understand more.

<br>
