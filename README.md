# terracotta-thirdparty-bom
Maven BOM for upgrading 3rd party dependencies
----

# Caveat:

All `<exclusion>` blocks should be done here when possible. 
You cannot override and add an exclusion in a child project's `<dependencyManagement>` block.

## For example, this will not work:

**BOM (here):**
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.eclipse.jetty</groupId>
            <artifactId>jetty-servlet</artifactId>
            <version>${jetty.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

**child project:**
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.terracotta</groupId>
            <artifactId>thirdparty-bom-4.x</artifactId>
            <version>XXX</version>
            <scope>import</scope>
            <type>pom</type>
        </dependency>
        
        <dependency>
            <groupId>org.eclipse.jetty</groupId>
            <artifactId>jetty-servlet</artifactId>
            <exclusion>
                ...
            </exclusion>
        </dependency>
    </dependencies>
</dependencyManagement>
```
This will fail because the bom-specified jetty-servlet definition is overriden completely.   

## This will work:

**child project:**
```xml
<dependencies>
    <dependency>
        <groupId>org.terracotta</groupId>
        <artifactId>thirdparty-bom-4.x</artifactId>
        <version>XXX</version>
        <scope>import</scope>
        <type>pom</type>
    </dependency>
    
    <dependency>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-servlet</artifactId>
        <exclusion>
            ...
        </exclusion>
    </dependency>
</dependencies>
```
Because `<dependencies>` section is cumulative.