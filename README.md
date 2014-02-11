# avro-gradle-plugin

A Gradle plugin for processing Avro files

---

Table of Contents

* <a href="#Usage">Usage</a>
* <a href="#Development">Development</a>
* <a href="#Contributing">Contributing to avro-gradle-plugin</a>
* <a href="#License">License</a>
* <a href="#Credits">Credits</a>

---

<a name="Usage"></a>

# Usage

This section demonstrates how to configure Gradle to integrate the compilation of Avro source files as part of a build.


## Configuring your Gradle build to use this plugin

The core information of the plugin (e.g. groupId, artifactId, latest version) is available at
[https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin).

The rest of this section shows how to configure Gradle beyond this core information so that you can actually use it in
practice.  The example `build.gradle` shown below assumes that:

* Avro source files are stored in `src/main/avro/`.
* You are using Avro 1.7.6 for compiling the Avro source files (if you are not change the config as appropriate)

Update your `build.gradle` to include the following configuration:

    buildscript {
        repositories {
            mavenCentral()
            mavenRepo url: "http://clojars.org/repo"
        }
        dependencies {
            classpath 'org.apache.maven:maven-artifact:2.2.1' // 3.x won't work
            classpath 'org.apache.avro:avro-compiler:1.7.6' // use Avro 1.7.6 to compile the Avro files
            classpath 'org.clojars.miguno:avro-gradle-plugin:1.7.2'
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

    $ gradle clean build

As part of the normal build process gradle will generate all the required Avro files and automatically add the relevant
class files to your project's build artifact (by default, this is a jar file under `build/libs/`).

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

Finally you can run the build, followed by pushing the build artifact to Clojars.org.

    $ ./gradlew clean build
    $ scp pom.xml ./build/libs/avro-gradle-plugin-*.jar clojars@clojars.org:

See [https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin)
for details.


<a name="Contributing"></a>

# Contributing to avro-gradle-plugin

Code contributions, bug reports, feature requests etc. are all welcome.

If you are new to GitHub please read [Contributing to a project](https://help.github.com/articles/fork-a-repo) for how
to send patches and pull requests to avro-gradle-plugin.


<a name="License"></a>

# License

Copyright © 2013-2014 Michael G. Noll.  Initial code written by Steve Holmes (see _Credits_ section below).

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
