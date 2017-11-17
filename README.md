# loom-os
Loom with pre-installed OpenStack adapter

This project is configured to include the version of Loom specified in `pom.xml` and the required versions of the adapter(s) which will be placed in the `./adapters` folder.  Assumes artifacts are available locally.  See the [Loom github repo](https://github.com/HewlettPackard/loom) for more details on Loom.

## Building 
Install Java 1.8 - note: OpenJDK has been tested but not used in production 

Install maven v3.0.5+ 

To build:

```
$ mvn clean install
```

## Configuring
This project includes the OpenStack adapter as well as a small test file adapter which can be removed/disabled from loading by removing/renaming the `./adapters/myadapter.properties` file.

The pre-installed OpenStack adapter needs configuraing to point to an OpenStack instance.  This is done by modifying the `./adapters/os.propertes` file.  Specifically you need to provide at a minimum a Keystone endpoint URI.

Loom will connect to multiple OpenStack instances if you define additional `.properties` files hosted in the same folder.

Normally Loom will start collecting data from OpenStack when a user logs in.  For large systems this can cause large delays because the OpenStack APIs are very slow.  Set `globalUser` and `globalPassword` to establish a long running background task for collection of data.  This data is then used instead when a normal user logs in.  Obviously for this to be effective the credentials need to belong to an account that access the data of multiple tenants.

See the example `*.properties` files for more information. 

## Running
You will need to reference `jetty-runner.jar` and `loom-server.war` in order to run.  The required versions are stated in the `jetty-version` and `loom-version` variables in `pom.xml`.  The provided `deployment.properties` file must be used.

```
$ java -Ddeployment.properties=deployment.properties -jar target\jetty-runner.jar --config jetty.xml target\loom-server.war
```

If you wish to modify the logging output you may override the `log4j.properties` file packaged with Loom by specifying your own when you launch Loom, e.g.

```
$ java -Dlog4j.configuration=file:my-log4j.properties -Ddeployment.properties=deployment.properties -jar target\jetty-runner.jar --config jetty.xml target\loom-server.war
```

An example log4j configuration is provided in `example-log4j.properties`.

Depending on the demands of the loaded adapters, i.e. the amount of data managed by Loom, you will need to increase the default JVM heap size and preallocate for best performance, e.g.

```
$ java -Xmx2048m -Xms2048m -XX:MaxPermSize=512m -Ddeployment.properties=deployment.properties -jar target\jetty-runner.jar --config jetty.xml target\loom-server.war
```

## Accessing Loom
The Weaver UI can be accessed via http://localhost:9099/weaver

## Using different versions
While not recommended, if you wish to experiment with other versions of Loom, Weaver or the adapters(s) then you need to modify `pom.xml` to ensure that the correct versions are included in the maven build.
