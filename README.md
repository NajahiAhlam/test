This error means **your Spring Boot app is calling a JAX-WS SOAP client**, but **the JAX-WS implementation (Metro / com.sun.xml.ws) is missing from the classpath**.

Specifically, these two classes are missing:

```
org.glassfish.hk2.osgiresourcelocator.ServiceLoader
com.sun.xml.ws.spi.ProviderImpl
```

Both come from **Metro JAX-WS** (NOT included in Java 11+ and NOT included by Spring Boot).

---

# \u2705 Why this happens

Java 8 included JAX-WS (javax.xml.ws).
Java 11+ **removed** all JAX-WS classes.

You are using:

```
jakarta.xml.ws.Service
jakarta.xml.ws.spi.Provider
```

\u2192 But **you did not include the Metro/JAX-WS implementation**.

So your generated SOAP client tries to load:

* `com.sun.xml.ws.spi.ProviderImpl` (Metro SPI)
* `org.glassfish.hk2.osgiresourcelocator.ServiceLoader` (needed by Metro inside OSGi)

And it fails \u2192 **ClassNotFoundException**.

---

# \u2705 Solution: Add the correct Metro dependencies

Add these to your **pom.xml**:

```xml
<dependency>
    <groupId>com.sun.xml.ws</groupId>
    <artifactId>rt</artifactId>
    <version>3.0.2</version>
</dependency>

<dependency>
    <groupId>org.glassfish.hk2</groupId>
    <artifactId>osgi-resource-locator</artifactId>
    <version>1.0.1</version>
</dependency>
```

If your client was generated with **JAX-WS 2.x (javax)** instead of **jakarta.xml**, use:

```xml
<dependency>
    <groupId>com.sun.xml.ws</groupId>
    <artifactId>jaxws-rt</artifactId>
    <version>2.3.5</version>
</dependency>
```

---

# \u2757 Check which version you need

Look at this:

```
jakarta.xml.ws.Service
```

\u2192 You are using **Jakarta JAX-WS 3.x** (Jakarta namespace), so you MUST use:

```
com.sun.xml.ws:rt:3.x
```

NOT the old `jaxws-rt:2.x`.

---

# If you generated client classes with wsimport (javax) but use jakarta in code

You will get:

```
Provider com.sun.xml.ws.spi.ProviderImpl not found
```

In that case:

\u2714 Convert your generated sources to **Jakarta namespace**
OR
\u2714 Generate the client again using Jakarta-compatible wsimport

---

# \U0001f4cc Final checklist

Make sure your pom has:

```xml
<dependency>
    <groupId>com.sun.xml.ws</groupId>
    <artifactId>rt</artifactId>
    <version>3.0.2</version>
</dependency>

<dependency>
    <groupId>org.glassfish.hk2</groupId>
    <artifactId>osgi-resource-locator</artifactId>
    <version>1.0.1</version>
</dependency>
```

Then **clean + rebuild**:

```
mvn clean install
```

Restart the app.

---

# If you want, paste your **pom.xml** and I will tell you exactly what is missing.
