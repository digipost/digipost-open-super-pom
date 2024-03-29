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
    <version>12</version>
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


## Analyze declared dependencies vs. actually used

You can enable the profile `dependency-analyze` to include maven-dependency-plugin in your
build, configured to check if your declared dependencies match with what is actually used.

Add profile (or append separated with a `,`) in `.mvn/maven.config`:
```
-P dependency-analyze
```


## Check license headers in source files

Add the contents you would like to use as a license header in your source files
to the file `src/main/license-header.txt` (e.g. [this](examples/license-header.txt)),
and enable the profile `check-license-header` in your build.

Add profile (or append separated with a `,`) in `.mvn/maven.config`:
```
-P check-license-header
```

You can also add additional excludes by configuring the license-maven-plugin
in `<pluginManagement>`:
```xml
<plugin>
  <groupId>com.mycila</groupId>
  <artifactId>license-maven-plugin</artifactId>
  <configuration>
    <licenseSets>
      <licenseSet>
        <excludes>
          <exclude>src/somthing/*.*</exclude>
          <exclude>something/**/*</exclude>
        </excludes>
      </licenseSet>
    </licenseSets>
  </configuration>
</plugin>
```


## Generate and check NOTICE file

Add the template you would like to use for a
[NOTICE](https://www.apache.org/licenses/LICENSE-2.0.html#redistribution) file
to be included in the repository to `src/NOTICE.template`. E.g. [this](examples/NOTICE.template).

Add the profile `include-NOTICE` (or append separated with `,`) in `.mvn/maven.config`:
```
-P include-NOTICE
```

Generate NOTICE file with the command `mvn notice:generate` (this should be committed
to the repository). The build will include `notice:check` which validates that the
NOTICE file is up to date according to dependencies of the project.


## Generate report on API changes

This POM contains base config for [japicmp-maven-plugin](https://siom79.github.io/japicmp/) which can be used to
generate a report showing API changes and how you should version a new release. This config is not active by default,
but contained in the profile named `japicmp`, and you may want to complement the config by specifying which package
to include in the report in `<pluginManagement>`:

```xml
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
