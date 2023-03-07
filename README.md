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
    <version>9</version>
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

This mechanism [requires at least Maven 3.3.1](https://maven.apache.org/docs/3.3.1/release-notes.html).


## Generate report on API changes

This POM contains base config for [japicmp-maven-plugin](https://siom79.github.io/japicmp/) which can be used to
generate a report showing API changes and how you should version a new release. This config is not active by default,
but contained in the profile named `japicmp`, and you may want to complement the config by specifying which package
to include in the report:

```xml
<profiles>
    <profile>
        <id>japicmp</id>
        <build>
            <defaultGoal>japicmp:cmp</defaultGoal>
            <pluginManagement>
                <plugins>
                    <plugin>
                        <groupId>com.github.siom79.japicmp</groupId>
                        <artifactId>japicmp-maven-plugin</artifactId>
                        <configuration>
                            <parameter>
                                <includes>
                                    <include>no.digipost.signature</include>
                                </includes>
                            </parameter>
                        </configuration>
                    </plugin>
                </plugins>
            </pluginManagement>
        </build>
    </profile>
</profiles>
```

The report can be generated using the command:
```sh
mvn -Pjapicmp -Dproject.previousVersion=X.Y
```
(replace `X.Y` with the actual version you want to compare with)


## Release artifacts

With this parent pom you can now release to Sonatype nexus with automatic close and release from staging 
repository. This eliminates the need to manually logging in and click close and release. 

To perform a release you need to set a version in the project. Either you use set it or if you have
multiple modules, you can use `mvn versions:set -DnewVersion=$RELEASE_VERSION`.

If you commit that change or not, that is up to you. We will in the future skip that commit.

You can then perform the release: `mvn clean deploy --activate-profiles build-sources-and-javadoc,sign-artifacts,release`

## License

Projects are released under [The Apache Software License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.txt), unless stated otherwise by an individual project.
