avro-gradle-plugin
==================

Gradle Plugin for processing Avro files

This project is a fork of the [original avro-gradle-plugin](https://github.com/iamsteveholmes/avro-gradle-plugin) by
[Steve Holmes](https://github.com/iamsteveholmes).  On a high-level what I added to the original code is:

* The Avro plugin for Gradle is published and made available via the public Clojars.org maven repository
  at [https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin).
  This means that you don't have to build this plugin yourself anymore in order to use it.
* Documentation on how to actually configure your ``build.gradle`` to make use of this plugin.


# How to use

This section demonstrates how to configure Gradle to integrate the compilation of Avro source files as part of a build.


## Configuring Gradle

The core information of the plugin (e.g. groupId, artifactId, latest version) is available at
[https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin).

The rest of this section shows how to configure Gradle beyond this core information so that you can actually use it in
practice.  The example ``build.gradle`` shown below assumes that:

* Avro source files are stored in ``src/main/avro/``.
* You are using Avro 1.7.4 for compiling the Avro source files (if you are not change the config as appropriate)

Update your ``build.gradle`` to include the following configuration:

    buildscript {
        repositories {
            mavenCentral()
            mavenRepo url: "http://clojars.org/repo"
        }
        dependencies {
            classpath 'org.apache.maven:maven-artifact:2.2.1' // 3.x won't work
            classpath 'org.apache.avro:avro-compiler:1.7.4' // use Avro 1.7.4 to compile the Avro files
            classpath 'org.clojars.miguno:avro-gradle-plugin:1.7.2'
        }
    }

    apply plugin: 'avro-gradle-plugin'

    sourceCompatibility = 1.6
    targetCompatibility = 1.6

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

    $ ./gradlew clean build

As part of the normal build process gradle will generate all the required Avro files and automatically add the relevant
class files to your project's build artifact (by default, this is a jar file under ``build/libs/``).

Here is an overview of the Avro-related files being generated:

    # Java sources *.java of generated Avro classes
    generated-sources/avro/

    # Java binaries *.classes of generated Avro classes
    # (this is the same directory where Gradle will place all other compiled Java classes as well)
    build/classes/main/

Note that ``./gradlew clean`` will not automatically delete files under the ``generated-sources`` directory tree.  You
must do this manually if needed.


# How to release the plugin

First, you must update the version identifier in ``build.gradle`` and ``pom.xml`` (they MUST MATCH).

Then you can run the build, followed by pushing the build artifact to Clojars.org.

    $ ./gradlew clean build
    $ scp pom.xml ./build/libs/avro-gradle-plugin-*.jar clojars@clojars.org:

See [https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin)
for details.
