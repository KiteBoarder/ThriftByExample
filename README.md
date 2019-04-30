# ThriftByExample
Thrift by Example explained in details.<br/>
<br/>
This original example is here: https://thrift-tutorial.readthedocs.io/en/latest/usage-example.html<br/>
<br/>
Install thrift locally. On mac:
```
brew install thrift 
```
make sure the thrift version is 0.12.0 or more. 

To compile:
```
mvn clean compile
```

To run the server:
```
mvn exec:java -Dexec.mainClass="tutorial.MultiplicationServer"
```
In another tab run the client:
```
mvn exec:java -Dexec.mainClass="tutorial.MultiplicationClient"
```
(You should see the result 3*5=15)<br/>
<br/>
<br/>
==================================================<br/>
# Manual steps 
Details how to how to create this project manually, generate pom file, etc (ie not using git clone to copy this repository).
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
create a folder under src/main called thrift. <br/>
create a file named multiple.thrift in src/main/thrift: 
```
namespace java tutorial
namespace py tutorial

typedef i32 int // We can use typedef to get pretty names for the types we are using
service MultiplicationService
{
        int multiply(1:int n1, 2:int n2),
}
```

Create these 3 files in src/main/java:<br/>
Note: You need to add package tutorial to all 3 files, to be in the same package as the thrift generated java files. <br/><br/>
MultiplicationHandler.java:
```

package tutorial;
import org.apache.thrift.TException;

public class MultiplicationHandler implements MultiplicationService.Iface {

	@Override
	 public int multiply(int n1, int n2) throws TException {
	    System.out.println("Multiply(" + n1 + "," + n2 + ")");
	    return n1 * n2;
	 }
}
```
MultiplicationServer.java:
```
package tutorial;
import org.apache.thrift.server.TServer;
import org.apache.thrift.server.TServer.Args;
import org.apache.thrift.server.TSimpleServer;
import org.apache.thrift.transport.TServerSocket;
import org.apache.thrift.transport.TServerTransport;

public class MultiplicationServer {

  public static MultiplicationHandler handler;

  public static MultiplicationService.Processor processor;

  public static void main(String [] args) {
    try {
      handler = new MultiplicationHandler();
      processor = new MultiplicationService.Processor(handler);

      Runnable simple = new Runnable() {
        public void run() {
          simple(processor);
        }
      };      
     
      new Thread(simple).start();
    } catch (Exception x) {
      x.printStackTrace();
    }
  }

  public static void simple(MultiplicationService.Processor processor) {
    try {
      TServerTransport serverTransport = new TServerSocket(9090);
      TServer server = new TSimpleServer(new Args(serverTransport).processor(processor));

      System.out.println("Starting the simple server...");
      server.serve();
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
 
}
```
MultiplicationClient.java:
```
package tutorial;
import org.apache.thrift.TException;
import org.apache.thrift.protocol.TBinaryProtocol;
import org.apache.thrift.protocol.TProtocol;
import org.apache.thrift.transport.TSocket;
import org.apache.thrift.transport.TTransport;

public class MultiplicationClient {
  public static void main(String [] args) {

   
    try {
      TTransport transport;
     
      transport = new TSocket("localhost", 9090);
      transport.open();

      TProtocol protocol = new  TBinaryProtocol(transport);
      MultiplicationService.Client client = new MultiplicationService.Client(protocol);

      perform(client);

      transport.close();
    } catch (TException x) {
      x.printStackTrace();
    } 
  }

  private static void perform(MultiplicationService.Client client) throws TException
  {
    int product = client.multiply(3,5);
    System.out.println("3*5=" + product);
  }
}
```
