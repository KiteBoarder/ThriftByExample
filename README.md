# ThriftByExample
Thrift by Example in details<br/>
<br/>
This example is taken from https://thrift-tutorial.readthedocs.io/en/latest/usage-example.html<br/>
<br/>
To compile:
```
mvn clean compile
```

To run the server:
```
mvn exec:java -Dexec.mainClass
```
In another tab run the client:
```
mvn exec:java -Dexec.mainClass="tutorial.MultiplicationClient"
```
(You should see the result 3*5=15)<br/>
<br/>
<br/>
==================================================<br/>
More details to how to create files manually, generate pom file, etc.
```
mvn archetype:generate -DgroupId=tutorial -DartifactId=thriftDemo \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DarchetypeVersion=1.4 -DinteractiveMode=false
```

modify pom file:<br/>
- add following dependency:
```
<dependency>
  <groupId>org.apache.thrift</groupId>
  <artifactId>libthrift</artifactId>
  <version>0.12.0</version>
</dependency>
```
- Add the maven-thrift-plugin and maven-compiler-plugin in the build section:
```
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.thrift.tools</groupId>
                <artifactId>maven-thrift-plugin</artifactId>
                <version>0.1.11</version>
                <configuration>
                    <thriftExecutable>/usr/local/bin/thrift</thriftExecutable>
                    <generator>java</generator>
                </configuration>
                <executions>
                    <execution>
                        <id>thrift-sources</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>thrift-test-sources</id>
                        <phase>generate-test-sources</phase>
                        <goals>
                            <goal>testCompile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
```
