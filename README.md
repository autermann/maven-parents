# Parent POM's for 52°North [![Build Status](https://travis-ci.org/52North/maven-parents.svg?branch=master)](https://travis-ci.org/52North/maven-parents)

## Usage

To apply the 52°North parent pom in your project, add the following to your project's `pom.xml` file.
```xml
<parent>
    <groupId>org.n52</groupId>
    <artifactId>parent</artifactId>
    <version>1-SNAPSHOT</version>
</parent>

<!-- TODO remove this once org.n52:parent is released -->
<repositories>
    <repository>
        <id>sonatype-nexus-snapshots</id>
        <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>false</enabled>
        </releases>
    </repository>
</repositories>
```

### Define additional repositories in your pom file
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-enforcer-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>enforce</goal>
            </goals>
            <configuration>
                <rules>
                   <requireNoRepositories>
                    <!-- Allow repositories -->
                       <banRepositories>false</banRepositories>
                       <!-- Allow SNAPSHOT repositories -->
                       <allowSnapshotRepositories>true</allowSnapshotRepositories>
                       <!-- Define allowed repositories, use repo id -->
                       <allowedRepositories>
                            <id>repo ids</id>
                       </allowedRepositories>
                   </requireNoRepositories>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### Ignore specific dependencies from analysis

If you use only constants from a dependency, you may have to declare it explicitly as used:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <configuration>
    	<usedDependencies>
    		<usedDependency>${groupId}:${artifactId}</usedDependency>
    	</usedDependencies>
    </configuration>
</plugin>
```

### Unused listed dependencies

If you have dependencies that are used only at runtime, e.g. SAXON XSLT processor or a logging framework such as log4j encapsuled by slf4j, then you must include these dependencies as `runtime` as shown in the example below. Otherwise the enforcer plugin will complain about unused dependencies.

```xml
[..]
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>${version.log4j}</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>net.sf.saxon</groupId>
    <artifactId>Saxon-HE</artifactId>
    <version>9.6.0-7</version>
    <scope>runtime</scope>
</dependency>
[..]
```


## Deployment

Put your [Sonatype OSS](https://oss.sonatype.org/) credentials into your `.m2/settings.xml` like this:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                              http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <servers>
        <server>
            <id>ossrh</id>
            <username>$username</username>
            <password>$password</password>
        </server>
    </servers>
</settings>
```

### Snapshot

Shouldn't be needed in most cases, as `master` is automatically deployed, but just in case:
```sh
mvn deploy
```

### Release
```sh
mvn release:prepare
mvn release:perform -P sign
```
