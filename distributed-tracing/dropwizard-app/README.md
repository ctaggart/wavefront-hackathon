# Wavefront Hackthon - Dropwizard App

This is a sample java application using Dropwizard called beachshirts (#[beachops](https://medium.com/@matthewzeier/thoughts-from-an-operations-wrangler-how-we-use-alerts-to-monitor-wavefront-71329c5e57a8)) which makes cool shirts for beach. 


## Running Application locally 

- Make sure Jaeger is running on your env using the [Docker image](https://www.jaegertracing.io/docs/getting-started):

    ```bash
    $ docker run -d --name jaeger \
      -e COLLECTOR_ZIPKIN_HTTP_PORT=9411 \
      -p 5775:5775/udp \
      -p 6831:6831/udp \
      -p 6832:6832/udp \
      -p 5778:5778 \
      -p 16686:16686 \
      -p 14268:14268 \
      -p 9411:9411 \
      jaegertracing/all-in-one:1.8
    ```

- Run `mvn clean install` from the root directory of the project.

- Now run all the services using below commands from root directory of the project.
    ```bash
    java -jar ./shopping/target/shopping-1.0-SNAPSHOT.jar server ./shopping/app.yaml
    java -jar ./styling/target/styling-1.0-SNAPSHOT.jar server ./styling/app.yaml
    java -jar ./delivery/target/delivery-1.0-SNAPSHOT.jar server ./delivery/app.yaml
    ```

- Use `./loadgen.sh {interval}` in the root directory to send a request of ordering shirts every `{interval}` seconds. You will see some random failures which is added by us randomly.

- Now go to Jaeger UI (http://localhost:16686, if you're using all-in-one docker image as given above) and look for traces for service "shopping" and click on Find Traces.

## Change from Jaeger to Wavefront

- Add dependency of `Wavefront Opentracing SDK`:

  ```xml
  <dependency>
  	<groupId>com.wavefront</groupId>
  	<artifactId>wavefront-opentracing-sdk-java</artifactId>
  	<version>0.9.1</version>
  </dependency>
  ```

- Go to `dropwizard-app/common/../Tracing.java`, make change of `Tracer init(String service)` method to return [WavefrontTracer](https://github.com/wavefrontHQ/wavefront-opentracing-sdk-java#set-up-a-tracer) instead of `com.uber.jaeger.Tracer`. Now all the tracing data is sending to Wavefront
