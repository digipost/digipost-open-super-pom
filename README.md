[![Maven Central](https://maven-badges.herokuapp.com/maven-central/no.digipost/digipost-open-super-pom/badge.svg)](https://maven-badges.herokuapp.com/maven-central/no.digipost/digipost-open-super-pom)

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
    <version>7</version>
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

## Release artifacts

With this parent pom you can now release to Sonatype nexus with automatic close and release from staging 
repository. This eliminates the need to manually logging in and click close and release. 

To perform a release you need to set a version in the project. Either you use set it or if you have
multiple modules, you can use `mvn versions:set -DnewVersion=$RELEASE_VERSION`.

If you commit that change or not, that is up to you. We will in the future skip that commit.

You can then perform the release: `mvn clean deploy --activate-profiles build-sources-and-javadoc,sign-artifacts,release`

## License

Projects are released under [The Apache Software License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.txt), unless stated otherwise by an individual project.
