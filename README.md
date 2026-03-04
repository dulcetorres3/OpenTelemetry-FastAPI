# OpenTelemetry-FastAPI
This repo is a basic example of how to integrate OpenTelemetry tracing into a simple app with Jaeger as the observation UI. It send traces directly to jaeger rather than using an OTel collector in between.

# Prerequisites 
* Docker

# Setup
Install starter dependancies.
```
pip install requirements.txt
```

Install OpenTelemetry distirbution. This will instll the API, SDK, bootstrap, and instrument.
```
pip install opentelemetry-distro
```

Implement bootstrap. This will download dependancy libraries.
```
opentelemetry-bootstrap -a install 
```

Kickoff installed OpenTelemetry.
```
export OTEL_PYTHON_LOGGING_AUTO_INSTRUMENTATION_ENABLED=true
opentelemetry-instrument \
    --traces_exporter console \
    --metrics_exporter console \
    --logs_exporter console \
    --service_name dice_server \
    uvicorn main:app
```
Setting OTEL_PYTHON_LOGGING_AUTO_INSTRUMENTATION_ENABLED to true prints all of logging/tracing to console. OpenTelemetry's instrument will then enable tracing, metrics, and logs.

Implemenet UI to see the information in a more readable format. Install jaeger tracing all in one at the latest image.
```
docker run --rm \
-e COLLECTOR_ZIPKIN_HOST_PORT=:9411 \
-p 16686:16686 \
-p 4317:4317 \
-p 4318:4318 \
-p 9411:9411 \
jaegertracing/all-in-one:latest
```
Jaeger UI will be at http://localhost:16686/search

Configure Jaeger UI with FastAPI app. Keep the docker terminal running and open a different terminal. Install the exporter in this new terminal and establish the service name for your app.
```
pip install opentelemetry-exporter-otlp-proto-grpc
opentelemetry-instrument --service_name roll.dice uvicorn main:app
```

Go back to the Jaeger UI and refresh.  You will see jaeger-all-in-one as a service.
Go back to app UI and refresh three times.
Go back to the Jaeger UI and refresh. You will now be able to see the roll.dice service. Clicking on this service will allow you to see all traces for the API endpoint.

