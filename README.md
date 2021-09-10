# terracotta-thirdparty-bom
Maven BOM for upgrading 3rd party dependencies
----

# Purpose

To bottleneck all third-party version numbers in one place, simplifying security updates.

# Limitations

The BOM (this project) only provides dependency version specification to downstream projects.
It does not provide properties or versions for plugins.  
In cases where both are needed for the same version value, duplication is inevitable.

# Making Changes

1. Make changes on the appropriate branch
2. `mvn install`, then test downstream projects locally (SNAPSHOT version)
3. PR or commit
4. SNAPSHOT version is deployed automatically
5. Kit pipeline is triggered automatically

No manual releasing is necessary, the tip of the branch will be tagged and released at kit build time.

# Downstream Project Usage

Add the following block:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.terracotta</groupId>
            <artifactId>thirdparty-bom-4.x</artifactId>
            <version>X.Y.Z-SNAPSHOT</version>
            <scope>import</scope>
            <type>pom</type>
        </dependency>

        ...

    </dependencies>
</dependencyManagement>
```

Remove `<version>` element from all third-party `<dependency>` blocks.

# Caveat:

All `<exclusion>` blocks should be done here when possible. 
* You **cannot** add an `<exclusion>` in a child project's `<dependencyManagement>` block.
* You **can** add an `<exclusion>` in a child project's `<dependencies>` block.
    (Because `<dependencies>` section is cumulative, so `<version>` comes from the BOM)

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
This will fail because the bom-specified jetty-servlet definition is overriden completely 
and is thus missing a `<version>`.  Specifying a version would defeat the purpose.

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
Because `<dependencies>` section is cumulative, so `<version>` comes from the BOM
