* Generate project folder
  ```
    mvn archetype:generate -DgroupId=com.krish.apps -DartifactId=hello-app -DarchetypeArtifactId=maven-archetype-quickstart -Darchtypeversion=1.4 -DinteractiveMode=false
  ```
  
* update with java version
  ```
      <properties>
	<!-- https://maven.apache.org/general.html#encoding-warning -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
  ```
  
  * add some external dependency
    ```
        <dependency>
        	<groupId>commons-codec</groupId>
        	<artifactId>commons-codec</artifactId>
        	<version>1.11</version>
        </dependency>
    ```
    
  * write some code
    ```
         public static void main(String[] args) {

           if (args.length < 1) {
              System.err.println("Please provide an input!");
              System.exit(0);
           }
           System.out.println(sha256hex(args[0]));

         }

         public static String sha256hex(String input) {
           return DigestUtils.sha256Hex(input);
         }
    ```
  
  * build & package
    ```
       mvn package
    ```
    
* run the program, but will get error dependency not include file run jar 
  ```
       java -cp target/hello-app-1.0-SNAPSHOT.jar com.krish.apps.App 123
       Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/commons/codec/digest/DigestUtils
               	at com.krish.apps.App.sha256hex(App.java:20)
              	at com.krish.apps.App.main(App.java:15)
       Caused by: java.lang.ClassNotFoundException: org.apache.commons.codec.digest.DigestUtils
              	at java.base/jdk.internal.loader.BuiltinClassLoader.loadClass(BuiltinClassLoader.java:602)
              	at java.base/jdk.internal.loader.ClassLoaders$AppClassLoader.loadClass(ClassLoaders.java:178)
               	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:522)
          	... 2 more
  ```
* add dependencies inside jar (fatjar), add maven-shade-plugin
  ```
      <build>
        <plugins>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.0</version>
                <executions>
                    <!-- Attach the shade goal into the package phase -->
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
       </build>
 
  ```
* now build and run
  ```
     mvn clean package
     java -cp target/hello-app-1.0-SNAPSHOT.jar com.krish.apps.App 123456
  ```
* running jar as java app add following snipet to maven-shade-plugin (withing <execution></execution>)
  ```
    <configuration>
				<transformers>
					<transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
						<mainClass>com.krish.apps.App</mainClass>
					</transformer>
				</transformers>
			</configuration>
  
  ```
* build and run
  ```
     mvn clean package
     java -jar target/hello-app-1.0-SNAPSHOT.jar 1234
  ```



