# avro-gradle-plugin

A Gradle plugin for processing Avro files

---

Table of Contents

* <a href="#Usage">Usage</a>
* <a href="#Development">Development</a>
* <a href="#Changelog">Change log</a>
* <a href="#Contributing">Contributing to avro-gradle-plugin</a>
* <a href="#License">License</a>
* <a href="#Credits">Credits</a>

---

<a name="Usage"></a>

# Usage

This section demonstrates how to configure Gradle to integrate the compilation of Avro source files as part of a build.


## Available versions

The plugin is available from the [Clojars.org](https://clojars.org) repository at the URL
[https://clojars.org/repo](https://clojars.org/repo).

The latest version is:

    org.clojars.miguno:avro-gradle-plugin:1.7.7.2  # for Avro 1.7.7

Older versions:

    org.clojars.miguno:avro-gradle-plugin:1.7.6  # for Avro 1.7.6

You can find past versions by browsing the plugin's Clojars landing page at
[avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin)).

See the [CHANGELOG](CHANGELOG.md) as well as the
[commit history](https://github.com/miguno/avro-gradle-plugin/commits/master) for differences between versions.


## Supported configuration parameters

* `compileAvro.source`: Location of Avro files (e.g. `*.avsc` schema files)
* `compileAvro.destinationDir`: Destination directory for generated files
* `compileAvro.stringType`: Java type to be emitted for string schemas (default: `CharSequence`).  Possible values:
  `CharSequence`,
  `String` (giving you a [java.lang.String](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html)),
  `Utf8` (giving you a [Utf8](https://avro.apache.org/docs/1.7.7/api/java/org/apache/avro/util/Utf8.html))


## Example: Configuring your Gradle build to use this plugin

The rest of this section shows how to configure Gradle beyond this core information so that you can actually use it in
practice.  The example `build.gradle` shown below assumes that:

* Avro source files are stored in `src/main/avro/`.
* You are using Avro 1.7.7 for compiling the Avro source files (if you are not change the config as appropriate)

Update your `build.gradle` to include the following configuration:

    buildscript {
      repositories {
        mavenCentral()
        maven { url "http://clojars.org/repo" } // Required to automatically download this plugin
      }
      dependencies {
        classpath 'org.apache.maven:maven-artifact:2.2.1' // 3.x won't work
        classpath 'org.apache.avro:avro-compiler:1.7.7' // Use Avro 1.7.7 to compile the Avro files
        classpath 'org.clojars.miguno:avro-gradle-plugin:1.7.7.2'
      }
    }

    apply plugin: 'avro-gradle-plugin'

    // Change setting to 1.6 if you want to build with Java 6
    sourceCompatibility = 1.7
    targetCompatibility = 1.7

    dependencies {
      compileAvro
    }

    compileAvro {
      source = 'src/main/avro'
      destinationDir = file("generated-sources/avro")
      // Uncomment the following line to explicitly set the Java type
      // to be emitted for string schemas (here: java.lang.String).
      // stringType = 'String'
    }

    sourceSets {
      main {
        java {
          srcDir compileAvro.destinationDir
        }
      }
    }

Now you are ready to run a build with gradle as described in the next section.


## Running Gradle

Run the following two commands:

    $ gradle build

As part of the normal build process gradle will generate all the required Avro files and automatically add the relevant
class files to your project's build artifact (by default, this is a jar file under `build/libs/`).

You can also manually trigger a compilation of Avro files via the `compileAvro` task:

    $ gradle compileAvro

Here is an overview of the Avro-related files being generated in your code project:

    # Java sources *.java of generated Avro classes
    generated-sources/avro/

    # Java binaries *.classes of generated Avro classes
    # (this is the same directory where Gradle will place all other compiled Java classes as well)
    build/classes/main/

Note that `gradle clean` will not automatically delete files under the `generated-sources` directory tree.  You
must do this manually if needed.


<a name="Development"></a>

# Development

This section is only relevant to developers of this plugin.  As a mere user of the plugin you don't need to follow any
of this.


## How to release this plugin

First, you must update the version identifier in `gradle.properties` and `pom.xml` (they MUST MATCH).

* `gradle.properties`: update the value of the `version` setting
* `pom.xml`: update the value of the `<version>` setting

Second, check whether the actual Avro version we are using is correct.  Typically, we want to use the same version
identifier for this Gradle plugin as the Avro version we are compiling against.

* `build.gradle`: update the dependency `org.apache.avro:avro-compiler:TARGET_VERSION` if needed

Third make sure you have defined your `clojarsUsername` and `clojarsPassword` settings in your local
`~/.gradle/gradle.properties`:

```
# ~/.gradle/gradle.properties
clojarsUsername=YOUR_CLOJARS_USERNAME
clojarsPassword=YOUR_CLOJARS_PASSWORD
```

Fourth, make sure you create a release tag:

    # Example for version 1.7.7
    $ git tag -a 1.7.7 -m 'Release 1.7.7'
    $ git push origin 1.7.7


Finally you can run the build, followed by pushing the build artifact to Clojars.org.

    $ ./gradlew clean publish


See [https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin)
for details.


<a name="Changelog"></a>

# Change log

See [CHANGELOG](CHANGELOG.md).


<a name="Contributing"></a>

# Contributing to avro-gradle-plugin

Code contributions, bug reports, feature requests etc. are all welcome.

If you are new to GitHub please read [Contributing to a project](https://help.github.com/articles/fork-a-repo) for how
to send patches and pull requests to avro-gradle-plugin.


<a name="License"></a>

# License

Copyright © 2013-2015 Michael G. Noll.  Initial code written by Steve Holmes (see _Credits_ section below).

See [LICENSE](LICENSE) for licensing information.  We are using the same license as the inital project.


<a name="Credits"></a>

# Credits

This project is a fork of the [original avro-gradle-plugin](https://github.com/iamsteveholmes/avro-gradle-plugin) by
[Steve Holmes](https://github.com/iamsteveholmes).

On a high-level what I added to the original code is:

* The Avro plugin for Gradle is published and made available via the public Clojars.org maven repository
  at [https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin).
  This means that you don't have to build this plugin yourself anymore in order to use it.
* Documentation on how to actually configure your `build.gradle` to make use of this plugin.

Many thanks to Steve Holmes for creating the original version of the plugin!
