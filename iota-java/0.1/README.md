# IOTA Java client library

This is the **official** Java client library, which allows you to do the following:
- Create, import, export, and manage accounts
- Send transactions
- Promote and reattach pending transactions
- Request deposits into conditional deposit addresses (CDA)
- Listen to events
- Extend the library functionality with plug-ins
- Interact with an IRI node

To learn more about how the library works:

- See how you can [work with accounts](root://iota-java/0.1/how-to-guides/create-account.md).
- See how you can [send and receive](root://iota-java/0.1/how-to-guides/create-and-manage-cda.md) transactions.

Get the library at the [IOTA Java GitHub repository](https://github.com/iotaledger/iota-java).

:::warning:Beta software
The client libraries are currently in beta. Their use in production is not supported.
:::

Please report any issues in our [issue tracker](https://github.com/iotaledger/iota-java/issues/new-issue).

## Audience

This documentation is designed for people who are familiar with the Java programming language and object-oriented programming concepts. You should also be familiar with basic IOTA concepts such as [address reuse](root://dev-essentials/0.1/concepts/addresses-and-signatures.md#address-reuse), [bundles, and transactions](root://dev-essentials/0.1/concepts/bundles-and-transactions.md).

This guide is designed to let you quickly start exploring and developing applications with IOTA.

## Prerequisites

To use the IOTA Java client library, your computer must have the following minimum requirement:

* Java 6 (or higher)

## Download the library

The IOTA Java client library is available on [JitPack][https://jitpack.io/].

### Dependencies

The IOTA Java client library uses the following third-party libraries:

* [Retrofit 2](https://square.github.io/retrofit/) type-safe HTTP client: `2.3.0`
* [Simple Logging Facade for Java](http://www.slf4j.org/): `1.7.25`
* [Apache Commons Lang](http://commons.apache.org/proper/commons-lang/): `3.6`
* [Apache Commons IO](http://commons.apache.org/proper/commons-io/): `2.5`
* [Legion of the Bouncy Castle](https://www.bouncycastle.org/java.html) Java cryptography APIs: `1.58`

To download the IOTA Java client library and its dependencies, you can use one of the following options:

* Download the library with Gradle
* Download the library with Maven
* Download the library manually

### Download the library with Gradle

 1. Add the JitPack repository to your root `build.gradle` file

    ```gradle
    allprojects {
        repositories {
            maven { url 'https://jitpack.io' }
        }
    }
    ```

1. Add the iotaledger dependency to your module `build.gradle` file

    ```gradle
    dependencies {
        compile 'com.github.iotaledger:iota-java:1.0.0-beta3'
    }
    ```
    
### Download the library with Maven

1. Add the JitPack repository to your root `pom.xml` file
    ```xml
    <repositories>
        <repository>
            <id>jitpack.io</id>
            <url>https://jitpack.io</url>
        </repository>
    </repositories>
    ```

2. Add the iotaledger dependency to your module `pom.xml` file
    ```xml
    <dependency>
        <groupId>com.github.iotaledger.iota-java</groupId>
        <artifactId>jota</artifactId>
        <classifier>jar-with-dependencies</classifier>
        <version>[VERSION_INFORMATION]</version>
    </dependency>
    ```
    
3. Change the value of the `<version>` tag to either a release number or the first 10 characters of a Git commit hash:
`<version>a98de8ea50</version>` or `<version>1.0.0-beta3</version>`

**Note:** Find the latest version on the [JitPack](https://jitpack.io/#iotaledger/iota-java) page.

### Download the library manually

1. Clone or download the [GitHub repository](https://github.com/iotaledger/iota-java)

    Inside the project, you'll have the following directories:
    * jota
    * jota-parent

2. Reference the jota directory in your project

3. In the jota directory, run the following command:

    `mvn clean install`

You'll have a .jar file called `jota-[VERSION]-jar-with-dependencies.jar`, depending on your version of the library.

## Get Started

After you've downloaded the library, you can connect to an IRI node to send transactions to it and interact with the ledger.

To connect to a local IRI node, do the following:

```java
IotaAPI api = new IotaAPI.Builder().build();
GetNodeInfoResponse response = api.getNodeInfo();
```

To connect to a remote IRI node, do the following:

```java
IotaAPI api = new IotaAPI.Builder()
        .protocol("http")
        .host("URL OF THE REMOTE IRI NODE")
        .port("14265")
        .build();
GetNodeInfoResponse response = api.getNodeInfo();
```

**Note:** To separate your IRI node configuration from the implementation, you can also specify your IRI node configuration in a [**Java `.properties` file**](https://en.wikipedia.org/wiki/.properties) or as command line flags. These options are useful if you develop an open-source app which is deployed on a CI and don't want contributors to see the internal IRI node configuration.

**Example .properties files**

```java
iota.node.protocol=http
iota.node.host=127.0.0.1
iota.node.port=14265
```

Most API calls are **synchronous**. Therefore, we recommend that you call the API from a background thread or a worker thread to stop the API from blocking other threads such as the UI or the main thread.

## API reference

For a full list of API commands for the IOTA Java client library, go to the [GitHub page](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPICommands.html#enum.constant.summary).

Here are some of the most commonly used API functions:

- [`getTransactionsObjects`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getTransactionsObjects-java.lang.String:A-)
- [`findTransactionObjects`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#findTransactionObjects-java.lang.String:A-)
- [`getTransactionsObjects`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getTransactionsObjects-java.lang.String:A-)
- [`getLatestInclusion`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getLatestInclusion-java.lang.String:A-)
- [`broadcastAndStore`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#broadcastAndStore-java.lang.String...-)
- [`getNewAddress`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getNewAddress-java.lang.String-int-int-boolean-int-boolean-)
- [`getInputs`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getInputs-java.lang.String-int-int-int-long-)
- [`prepareTransfers`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#prepareTransfers-java.lang.String-int-java.util.List-java.lang.String-java.util.List-boolean-)
- [`sendTrytes`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#sendTrytes-java.lang.String:A-int-int-)
- [`sendTransfer`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#sendTransfer-java.lang.String-int-int-int-java.util.List-jota.model.Input:A-java.lang.String-)
- [`replayBundle`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#replayBundle-java.lang.String-int-int-)
- [`getBundle`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getBundle-java.lang.String-)
- [`getTransfers`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getTransfers-java.lang.String-int-java.lang.Integer-java.lang.Integer-java.lang.Boolean-)
- [`initiateTransfer`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#initiateTransfer-int-java.lang.String-java.lang.String-java.util.List-boolean-)
- [`getAccountData`](https://iotaledger.github.io/iota-java/javadoc/jota/IotaAPI.html#getAccountData-java.lang.String-int-int-boolean-int-boolean-int-int-boolean-long-)

## Examples

We have a list of test cases in the [`src/test/java` directory](https://github.com/iotaledger/iota-java/blob/dev/jota/src/test) that you can use as a reference when developing apps with IOTA.
A good place to start is the [`IotaAPITest` case](https://github.com/iotaledger/iota-java/blob/dev/jota/src/test/java/org/iota/jota/IotaAPITest.java).

## Change logs
- Changes in [**1.0.0-beta3**](https://github.com/iotaledger/iota-java/compare/1.0.0-beta2...1.0.0-beta3)
- Changes in [**1.0.0-beta2**](https://github.com/iotaledger/iota-java/compare/1.0.0-beta1...1.0.0-beta2)
- Changes in [**1.0.0-beta1**](https://github.com/iotaledger/iota-java/compare/0.9.10...1.0.0-beta1)
- Changes in [**v0.9.10**](https://github.com/iotaledger/iota-java/compare/v0.9.6...0.9.10)
- Changes in [**v0.9.6**](https://github.com/iotaledger/iota-java/compare/v0.9.5...v0.9.6)
- Changes in [**v0.9.5**](https://github.com/iotaledger/iota-java/compare/v0.9.4...v0.9.5)
- Changes in [**v0.9.4**](https://github.com/iotaledger/iota-java/compare/v0.9.3...v0.9.4)
- Changes in [**v0.9.3**](https://github.com/iotaledger/iota-java/compare/v0.9.2...v0.9.3)
- Changes in [**v0.9.2**](https://github.com/iotaledger/iota-java/compare/v0.9.1...v0.9.2)
- Changes in [**v0.9.1**](https://github.com/iotaledger/iota-java/commits/v0.9.1)

## Support the project

If the IOTA Java client library has been useful to you and you feel like contributing, consider posting a [bug report](https://github.com/iotaledger/iota-java/issues/new-issue), feature request or a [pull request](https://github.com/iotaledger/iota-java/pulls/).  
We have some [basic contribution guidelines](https://github.com/iotaledger/iota-java/blob/dev/CONTRIBUTING.md) to keep our code base stable and consistent.

## Join the Discussion

If you want to get involved in the community, need help with getting setup, have any issues related with the library or just want to discuss blockchain, distributed ledgers, and IoT with other people, feel free to join our [Discord](https://discord.iota.org).  

## License

The Apache 2.0 license can be found [here](https://github.com/iotaledger/iota-java/blob/dev/LICENSE).