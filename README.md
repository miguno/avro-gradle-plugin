avro-gradle-plugin
==================

Gradle Plugin for processing Avro files


# How to use

This section demonstrates how to configure Gradle to integrate the compilation of Avro source files as part of a build.

## Configuring Gradle

The example ``build.gradle`` shown below assumes that:

* Avro source files are stored in ``src/main/avro``.
* You are using Avro 1.7.4 for compiling the Avro source files (if you are not change the config as appropriate)


Update your ``build.gradle`` to include the following configuration:

    buildscript {
        repositories {
            mavenCentral()
            mavenRepo url: "http://clojars.org/repo"
        }
        dependencies {
            classpath 'org.apache.maven:maven-artifact:2.2.1' // 3.x won't work
            classpath 'org.apache.avro:avro-compiler:1.7.4'
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


## Running Gradle

Run the following two commands:

    $ gradle clean build

The compiled Avro classes will automatically be added to your build artifact (by default, this is a jar file under
``build/libs/``).

Here is an overview of the Avro-related files being generated:

    # Java sources *.java of generated Avro classes
    generated-sources/avro/

    # Java binaries *.classes of generated Avro classes
    # (this is the same directory where Gradle will place all other compiled Java classes as well)
    build/classes/main/

Note that ``gradle clean`` will not automatically delete files under the ``generated-sources`` directory tree.  You must
do this manually if needed.


# How to release the plugin

First, you must update the version identifier in ``build.gradle`` and ``pom.xml`` (they MUST MATCH).

Then you can run the build, followed by pushing the build artifact to Clojars.org.

    $ gradle clean build
    $ scp pom.xml ./build/libs/avro-gradle-plugin-*.jar clojars@clojars.org:

See [https://clojars.org/org.clojars.miguno/avro-gradle-plugin](https://clojars.org/org.clojars.miguno/avro-gradle-plugin)
for details.
