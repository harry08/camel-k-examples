# camel-k-examples
dev environment for camel-k examples

## Documentation
- https://camel.apache.org

## Remote Debugging
Remote debugging in Camel-k is possible but with some constraints. First of all, starting a remote
debugging session is straight forward, e.g. debugging the route sender example is done by
`kamel debug route-sender`. This command starts a remote debugging session default on 'port 5005'.
This port can be attached from within this project, e.g. a 'Remote JVM Debug' run configuration in IntelliJ.
The Camel DSL debugging is kind of senseless, since it only configures the behaviour. More interesting is
debugging a specific call to this route. For this, create a process step and put a breakpoint inside this
process method.

### Constraint
HotSwap is not supported with Camel-k

## Examples
### Transform
You can use OpenAPI v3. Over the `operation-id` you can connect operations form the OpenAPI YAML to the Camel DSL.  
To run the integration you have to do `kamel run ./src/main/java/ch/baloise/example/transform/TransformRoute.java`.
The `tls-termination=edge` is for OpenShift to enable HTTPS.

In general a lot of the OpenAPI spec is not respected like payload validations or return types/codes.  
It makes more sense to use https://camel.apache.org/manual/rest-dsl.html to define the API by DSL and generate the OpenAPI.

### OpenAPI
You can find the documentation under https://camel.apache.org/manual/rest-dsl.html for a code first example.  
The rest endpoints will be correctly deployed and exposed, see `example/openapi`. Sadly there is no access to
the OpenAPI files or UI, as inner classes are limiting these feature. Additionally, if you like to start the example
locally, you have to install a platform server (e.g. undertow, netty). 

### Transform partner json to contact json
To execute this example run the Camal Main app `./src/main/java/ch/baloise/example/contact/mapper/ContactMapperMainApp.java`
This route reads json files from the folder `./src/data/partner` and maps the incoming json eventually to a json representing a contact object.
Uses Camel marshalling and unmarshalling.

### Route exmaple

Example for calling a camel-k micro service from within another camel-k micro service. Used two easy open-api
micro services passing a paramter "name" which is concatenated by the second service call.
- `./src/main/java/ch/baloise/example/route/OpenApiSender.java`
- `./src/main/java/ch/baloise/example/route/OpenApiReceiver.java`

#### Findings
The rest parameter "name" is put into the message header by camel. In order to avoid a multiple definition
by the inner service call, it need to be removed.
Using setProperty() for variables only works inside the Exchange object but there is no way to access these with
getProperty() inside the same dsl route, e.g. in the toD :(.

### Simple HelloWorld JUnit Test
See `./src/test/java/ch/baloise/example/helloworld/HelloRoute.java`

Uses CamelTestSupport
