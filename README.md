# Digipost Open Source Project &ndash; Super-POM

Common configuration for
[Digipost open source projects](https://github.com/digipost). It enables deployment to
[Sonatype's staging and snapshot repository](https://oss.sonatype.org), and provides
some configuration of commonly used plugins.


## Usage

Add the following parent to your POM file:

```xml
<parent>
    <groupId>no.digipost</groupId>
    <artifactId>digipost-open-super-pom</artifactId>
    <version>2</version>
</parent>
```


## Build source and javadoc artifacts

Using this POM as parent will ensure that proper artifacts are built containing source files and
javadoc during release, which is required to be allowed to publish to the Maven Central Repository.
This is not enabled for snapshot builds by default, but can be enabled by using the profile
`build-sources-and-javadoc`. If you want this to be enabled for all your builds, you can create
the file `.mvn/maven.config` in your project with the following content:

```
-P build-sources-and-javadoc
```

This mechanism [requires at least Maven 3.3.1](https://maven.apache.org/docs/3.3.1/release-notes.html), and
you probably want to add the following to your `<build>`-&gt;`<plugins>` section of your POM to ensure this:

```xml
<plugin>
    <artifactId>maven-enforcer-plugin</artifactId>
    <executions>
        <execution>
            <id>enforce-maven</id>
            <goals>
                <goal>enforce</goal>
            </goals>
            <configuration>
                <rules>
                    <requireMavenVersion>
                        <!-- Maven 3.3.1 is needed for .mvn/maven.config to work
                             https://maven.apache.org/docs/3.3.1/release-notes.html -->
                        <version>3.3.1</version>
                    </requireMavenVersion>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```


## License

Projects are released under [The Apache Software License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.txt), unless stated otherwise by an individual project.
