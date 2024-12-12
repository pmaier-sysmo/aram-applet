# ARAM Applet

[![Build Status](https://travis-ci.org/bertrandmartel/aram-applet.svg?branch=master)](https://travis-ci.org/bertrandmartel/aram-applet)
[![Coverage Status](https://coveralls.io/repos/github/bertrandmartel/aram-applet/badge.svg?branch=master)](https://coveralls.io/github/bertrandmartel/aram-applet?branch=master)

JavaCard implementation of Global Platform Access Rule Application Master (ARA-M) applet according to
[Secure Element Access Control v1.1](https://globalplatform.org/specs-library/secure-element-access-control-v1-1) specification.

## What is this ?

ARA-M is an application (typically present on a SIM card) which manages access rules that are enforced by an Access Control Enforcer (typically present on [Android device](https://github.com/seek-for-android/pool/wiki)).
The enforcer makes sure the rules from the ARAM are enforced. An access rule is composed of:
* an AID
* a certificate hash (SHA-1 or SHA-256 hash of the client application certificate)
* a set of rules

The Access Control enforcer will allow/deny a client application (for example an Android app) to send APDU to a SE applet based on these rules

More information : [seek-for-android Access Control wiki](https://github.com/seek-for-android/pool/wiki/AccessControlIntroduction)

## Features

### Get Data

- [x] get all
- [x] get specific REF-DO
- [x] get refresh tag
- [x] get next

### Store Data

- [x] store REF-AR-DO
- [x] delete AID-REF-DO
- [x] delete REF-DO
- [ ] delete REF-AR-DO
- [x] update refresh tag

### Note

* store data can be accessed via install for personalization or via raw apdu STORE DATA
* get data length is coded on **2 bytes** max
* get specific is **not** compatible with get next
* rules are not stored as data object but as plain apdu AR-DO
* format of APDU-AR-DO, NFC-AR-DO is not checked
* deleting specific rules is not implemented (only aid/hash)

## Setup

#### Prepare the repository

```bash
git clone git@github.com:bertrandmartel/aram-applet.git
cd aram-applet
git submodule update --init
```

#### Build the applet

To build the applet, a valid combination of JDK and JAVA-card SDK must be used. This applet requires a combination of jc221_kit and jdk-8u421 (jdk1.8.0_421). A convenient way to select the JDK is to set the environment variable JAVA_HOME before calling ./gradlew.

* A table with compatible JDK / JAVA-card SDK combinations can be found here:  
<https://github.com/martinpaljak/ant-javacard/wiki/JavaCard-SDK-and-JDK-version-compatibility>

* Older JDK versions are available here:  
<https://www.oracle.com/de/java/technologies/javase/jdk11-archive-downloads.html>

```bash
JAVA_HOME=/path/to/your/jdk1.8.0_421/ ./gradlew build
```

#### build & install (will **delete** existing applet before install)

```bash
JAVA_HOME=/path/to/your/jdk1.8.0_421/ ./gradlew installJavaCard
```

## Tests

#### run simulation tests

```bash
JAVA_HOME=/path/to/your/jdk1.8.0_421/ ./gradlew test
```

#### run tests on smartcard

```bash
JAVA_HOME=/path/to/your/jdk1.8.0_421/ ./gradlew test -DtestMode=smartcard
```

## Scripts

### Install for personalization

#### list rules

```bash
gp -acr-list
```

Use [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro) to send store data via the Security Domain with install comand + install for personalization :

#### add rule

```bash
gp -acr-add -acr-rule 01 -app D2760001180002FF49502589C0019B18 -acr-hash 1FA8CC6CE448894C7011E23BCF56DB9BD9097432
```

#### delete rule

```bash
gp -acr-delete -app D2760001180002FF49502589C0019B18 -acr-hash 1FA8CC6CE448894C7011E23BCF56DB9BD9097432
```

### Raw APDU

#### list rules

```bash
JAVA_HOME=/path/to/your/jdk1.8.0_421/ ./gradlew list
```

The following task send store data command raw apdu via [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro) (for add & delete) :

#### add rule

```bash
./gradlew store
```

or

```bash
JAVA_HOME=/path/to/your/jdk1.8.0_421/ ./gradlew test --tests fr.bmartel.aram.AramTest.storeDataValid
```

#### delete rule

```bash
./gradlew delete
```
or
```bash
JAVA_HOME=/path/to/your/jdk1.8.0_421/ ./gradlew test --tests fr.bmartel.aram.AramTest.deleteByAid
```

## License

The MIT License (MIT) Copyright (c) 2017 Bertrand Martel