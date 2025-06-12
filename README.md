[ERROR] [ERROR] Some problems were encountered while processing the POMs:
[WARNING] 'dependencies.dependency.(groupId:artifactId:type:classifier)' must be unique: org.springframework.cloud:spring-cloud-starter-openfeign:jar -> duplicate declaration of version (?) @ line 197, column 21
[ERROR] 'dependencies.dependency.version' for org.springframework.cloud:spring-cloud-starter-sleuth:jar is missing. @ line 153, column 21
[WARNING] 'dependencies.dependency.scope' for org.keycloak.bom:keycloak-adapter-bom:pom must be one of [provided, compile, runtime, test, system] but is 'import'. @ line 233, column 20
[ERROR] The build could not read 1 project -> [Help 1]
[ERROR]
[ERROR]   The project ma.sgma:mycnp-bff:0.1.1-RC-202505261711 (C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff\pom.xml) has 1 error
[ERROR]     'dependencies.dependency.version' for org.springframework.cloud:spring-cloud-starter-sleuth:jar is missing. @ line 153, column 21
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
PS C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff> ^C
PS C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff> ^C
PS C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff> mvn clean install -DskipTests
[INFO] Scanning for projects...
[ERROR] [ERROR] Some problems were encountered while processing the POMs:
[FATAL] Non-parseable POM C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff\pom.xml: expected START_TAG or END_TAG not TEXT (position: TEXT seen ...ngframework.boot/spring-boot-starter-thymeleaf -->\r\n\r\n\r\n        <d... @208:11)  @
 line 208, column 11
[ERROR] The build could not read 1 project -> [Help 1]
[ERROR]
[ERROR]   The project  (C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff\pom.xml) has 1 error
[ERROR]     Non-parseable POM C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff\pom.xml: expected START_TAG or END_TAG not TEXT (position: TEXT seen ...ngframework.boot/spring-boot-starter-thymeleaf -->\r\n\r\n\r\n        <d... @208:11
)  @ line 208, column 11 -> [Help 2]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
[ERROR] [Help 2] http://cwiki.apache.org/confluence/display/MAVEN/ModelParseException
PS C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff> mvn clean install -DskipTests
[INFO] Scanning for projects...
PS C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff> mvn clean install -DskipTests
[INFO] Scanning for projects...
[ERROR] [ERROR] Some problems were encountered while processing the POMs:                                                                
[ERROR] 'dependencies.dependency.version' for org.springframework.cloud:spring-cloud-starter-sleuth:jar is missing. @ line 153, column 21
 @                                                                                                                                       
[ERROR] The build could not read 1 project -> [Help 1]                                                                                       
[ERROR]                                                                                                                                      
[ERROR]   The project ma.sgma:mycnp-bff:0.1.1-RC-202505261711 (C:\Users\NAJAHIA\Documents\mycnp\mycnp-bff\pom.xml) has 1 error               
[ERROR]     'dependencies.dependency.version' for org.springframework.cloud:spring-cloud-starter-sleuth:jar is missing. @ line 153, column 21
[ERROR]                                                                                                                                      
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.                                                          
[ERROR] Re-run Maven using the -X switch to enable full debug logging.                                                                       
[ERROR]                                                                                                                                      
[ERROR] For more information about the errors and possible solutions, please read the following articles:                                    
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.4.6</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>ma.sgma</groupId>
    <artifactId>mycnp-bff</artifactId>
    <version>0.1.1-RC-202505261711</version>
    <name>mycnp</name>
    <description>mycnp</description>
    <url/>
    <licenses>
        <license/>
    </licenses>
    <developers>
        <developer/>
    </developers>
    <scm>
        <connection/>
        <developerConnection/>
        <tag/>
        <url/>
    </scm>
    <properties>
        <sonar.projectVersion>0.110.18.SNAPSHOT</sonar.projectVersion>
        <java.version>17</java.version>
        <mapstruct.version>1.4.2.Final</mapstruct.version>
        <ms-bpm-client.version>0.12.3</ms-bpm-client.version>
        <jacoco-maven-plugin.version>0.8.2</jacoco-maven-plugin.version>
        <jacoco-plugin.version>0.8.3</jacoco-plugin.version>
        <spring-cloud.version>2024.0.1</spring-cloud.version>
        <spring-cloud-dependencies.version>2024.0.4</spring-cloud-dependencies.version>
        <sonar.exclusions>
            **/config/**,
            **/domain/**,
            **/handlerException/**,
            **/mapper/**,
            **/model/**,
            **/MycnpApplication.class
            <!--**/service/**-->
            <!--**/controllers/ApplicationInfos.java,
            **/services/impl/RepriseService.java,
            **/services/impl/AutomationServiceImpl.java,-->
            <!--**/WfAgiosBffApplication.java,-->
            <!--**/controllers/AgregateurController.java,-->
            <!--            **/services/**,-->
            <!--**/controllers/BatchRessource.java,-->
            <!--**/controllers/HolidaysRessource.java,-->
            <!--**/services/impl/JiraServiceImpl.java,-->
            <!--**/services/impl/UserService.java,-->
            <!--**/services/impl/RequestService.java,-->
            <!--**/services/impl/UserRessource.java-->
        </sonar.exclusions>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>javax.ws.rs</groupId>
            <artifactId>javax.ws.rs-api</artifactId>
            <version>2.1.1</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>com.sgma.ms.bpm</groupId>
            <artifactId>ms-bpm-client</artifactId>
            <version>${ms-bpm-client.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
            <version>1.5.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>


        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>


        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>itext7-core</artifactId>
            <version>7.2.3</version>
            <type>pom</type>
        </dependency>

        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>kernel</artifactId>
            <version>7.2.3</version>
        </dependency>


        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>itextpdf</artifactId>
            <version>5.5.13.3</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/org.mapstruct/mapstruct -->
        <!-- https://mvnrepository.com/artifact/org.keycloak/keycloak-adapter-core -->
        <!-- https://mvnrepository.com/artifact/org.keycloak/keycloak-spring-security-adapter -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <!--			<version>5.2.0</version>-->
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
            <version>5.8.2</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-kubernetes-fabric8-all</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-prometheus</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-netflix-ribbon</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.netflix.ribbon</groupId>
                    <artifactId>ribbon-eureka</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-netflix-hystrix</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-launcher</artifactId>
            <version>1.8.2</version>
            <scope>test</scope>
        </dependency>


        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-spring-boot-starter</artifactId>
            <version>24.0.1</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-thymeleaf -->


        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct</artifactId>
            <version>1.4.2.Final</version>
        </dependency>

        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-admin-client</artifactId>
            <version>12.0.4</version>
        </dependency>

        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct-processor</artifactId>
            <version>1.4.2.Final</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>${jacoco-maven-plugin.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-oauth2-client</artifactId>
            <version>2.7.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi</artifactId>
            <version>5.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml</artifactId>
            <version>5.0.0</version>
        </dependency>
        <dependency>
            <groupId>com.github.ozlerhakan</groupId>
            <artifactId>poiji</artifactId>
            <version>4.5.0</version>
        </dependency>
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-apt</artifactId>
        </dependency>
        <dependency> <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.12.3</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.jaxrs</groupId>
            <artifactId>jackson-jaxrs-json-provider</artifactId>
            <version>2.12.3</version>
        </dependency>

        <dependency> <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
        </dependency>

        <dependency>
            <groupId>org.modelmapper</groupId>
            <artifactId>modelmapper</artifactId>
            <version>3.2.0</version>
        </dependency>


    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>de.codecentric</groupId>
                <artifactId>spring-boot-admin-dependencies</artifactId>
                <version>2.1.5</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
            <plugin>
                <groupId>com.mysema.maven</groupId>
                <artifactId>apt-maven-plugin</artifactId>
                <version>1.1.3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>process</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>target/generated-sources/java</outputDirectory>
                            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>${jacoco-plugin.version}</version>
                <executions>
                    <execution>
                        <id>prepare-agent</id>
                        <goals>
                            <goal>prepare-agent</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>report</id>
                        <phase>prepare-package</phase>
                        <goals>
                            <goal>report</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>post-unit-test</id>
                        <phase>test</phase>
                        <goals>
                            <goal>report</goal>
                        </goals>
                        <configuration>
                            <!-- Sets the path to the file which contains the execution data. -->
                            <dataFile>target/jacoco.exec</dataFile>
                            <!-- Sets the output directory for the code coverage report. -->
                            <outputDirectory>target/my-reports</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
                <configuration>
                </configuration>
            </plugin>

        </plugins>
    </build>

</project>
