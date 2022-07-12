# Jcstress maven test

[Jcstress](https://github.com/openjdk/jcstress) is a tool that can be used to find concurrency errors in Java data 
structures. It is important to say that jcstress tries to look for bugs in the code, but it does not prove that the code
is correct. False negatives must be expected.

Jcstress is not designed to be executed in JUnit or any other testing framework. 
Instead, it has been designed to generate an uberjar that should be executed with `java -jar`.

Some people would like to enforce jcstress tests while the code is being build.
For example, [reyerizo/jcstress-gradle-plugin](https://github.com/reyerizo/jcstress-gradle-plugin) is a Gradle plugin
that adds a task called `jcstress` which executes the tests.
But there is a lack of Maven alternatives.
Maven plugins may be created, but given that there are none, this project is a very simple (and maybe incorrect) way to
run a jcstress tests in Maven.

This project is just a new Maven project created with
```bash
$ mvn archetype:generate \
 -DinteractiveMode=false \
 -DarchetypeGroupId=org.openjdk.jcstress \
 -DarchetypeArtifactId=jcstress-java-test-archetype \
 -DgroupId=org.sample \
 -DartifactId=test \
 -Dversion=1.0
```

Where [pom.xml] has been changed to use exec plugin to execute the jcstress uberjar:

```
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>3.0.0</version>
                <executions>
                    <execution>
                        <phase>integration-test</phase>
                        <goals>
                            <goal>exec</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <executable>java</executable>
                    <arguments>
                        <argument>-jar</argument>
                        <argument>../../jcstress.jar</argument>
                        <argument>-time</argument>
                        <argument>100</argument>
                    </arguments>
                    <workingDirectory>
                        target/reports/jcstress
                    </workingDirectory>
                </configuration>
            </plugin>
```

Also, [ConcurrencyTest](src/main/java/org/sample/ConcurrencyTest.java) has been changed to make it fail.

To run the test, simply run:
```
mvn verify
```

(Remember that `verify` is executed after `package` and before `install`)

