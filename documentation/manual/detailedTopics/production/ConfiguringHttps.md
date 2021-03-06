<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Configuring HTTPS

Play can be configured to serve HTTPS.  To enable this, simply tell Play which port to listen to using the `https.port` system property.  For example:

    ./start -Dhttps.port=9443

## SSL Certificates

### SSL Certificates from a keystore

By default, Play will generate itself a self-signed certificate, however typically this will not be suitable for serving a website.  Play uses Java key stores to configure SSL certificates and keys.

Signing authorities often provide instructions on how to create a Java keystore (typically with reference to Tomcat configuration).  The official Oracle documentation on how to generate keystores using the JDK keytool utility can be found [here](http://docs.oracle.com/javase/7/docs/technotes/tools/solaris/keytool.html).

Having created your keystore, the following system properties can be used to configure Play to use it:

* **https.keyStore** - The path to the keystore containing the private key and certificate, if not provided generates a keystore for you
* **https.keyStoreType** - The key store type, defaults to `JKS`
* **https.keyStorePassword** - The password, defaults to a blank password
* **https.keyStoreAlgorithm** - The key store algorithm, defaults to the platforms default algorithm

### SSL Certificates from a custom SSL Context

Another alternative to configure the SSL certificates is to provide a custom [SSLContext](http://docs.oracle.com/javase/7/docs/api/javax/net/ssl/SSLContext.html).

#### in Java, an implementation must be provided for [`play.server.SSLContextProvider`](api/java/play/server/SSLContextProvider.html)

@[javaexample](code/java/CustomSSLContextProvider.java)

#### in Scala, an implementation must be provided for [`play.server.api.SSLContextProvider`](api/scala/index.html#play.server.api.SSLContextProvider)

@[scalaexample](code/scala/CustomSSLContextProvider.scala)

Having created an implementation for `play.server.SSLContextProvider` or `play.server.api.SSLContextProvider`, the following system property configures Play to use it:

* **play.http.sslcontextprovider** - The path to the class implementing `play.server.SSLContextProvider` or `play.server.api.SSLContextProvider`:

Example:

    ./start -Dhttps.port=9443 -Dplay.http.sslcontextprovider=mypackage.CustomSSLContextProvider


## Turning HTTP off

To disable binding on the HTTP port, set the `http.port` system property to be `disabled`, eg:

    ./start -Dhttp.port=disabled -Dhttps.port=9443 -Dhttps.keyStore=/path/to/keystore -Dhttps.keyStorePassword=changeme

## Production usage of HTTPS

If Play is serving HTTPS in production, it should be running JDK 1.8.  JDK 1.8 provides a number of new features that make JSSE feasible as a [TLS termination layer](http://blog.ivanristic.com/2014/03/ssl-tls-improvements-in-java-8.html).  If not using JDK 1.8, using a [[reverse proxy|HTTPServer]] in front of Play will give better control and security of HTTPS.

* **[`SSLParameters.setUseCipherSuiteorder()`](http://download.java.net/jdk8/docs/technotes/guides/security/jsse/JSSERefGuide.html#cipher_suite_preference)** - Reorders cipher suite order to the server's preference.
* **-Djdk.tls.ephemeralDHKeySize=2048** - Increases the key size in a DH key exchange.
* **-Djdk.tls.rejectClientInitiatedRenegotiation=true** - Rejects client renegotiation.

> **Next:** [[Deploying to a cloud service|DeployingCloud]]
