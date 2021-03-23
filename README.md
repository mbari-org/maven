# maven

## MBARI's Public Maven Repository

This repository hosts MBARI's public [Maven](https://maven.apache.org/) artifacts. 

## Public Usage

GitHub packages requires authentication, even for public packages. To access MBARI public maven artifacts, you will need a [GitHub] account and a GitHub personal access token with at least `read:packages` scope.

### To create a personal access token:

1. Click on your user icon in GitHub and select `Settings`.
2. Select `Developer Settings` near the bottom of the left sidebar.
3. Select `Personal access token`.
4. Click on the `Generate new token` button on the top left.
5. Select your desired scopes. Be sure to include `read:packages` and then click the `Generate token` button near the bottom.
6. Copy your token and save it somewhere secure.


### Maven

#### Configure your personal access token

Open or create the file `~/.m2/settings.xml`. Then add the following:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" 
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0                          https://maven.apache.org/xsd/settings-1.0.0.xsd">


  <servers>
    
    <!-- Github access token section -->
    <server>
      <id>github</id>
      <username>your github username</username>
      <password>your github access token</password>
    </server>

  </servers>

</settings>

```

#### Configure pom.xml

```xml
<repositories>

    <repository>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
        <id>github</id>
        <name>github</name>
        <url>https://maven.pkg.github.com/mbari-org/maven</url>
    </repository>

</repositories>

```

### Gradle

#### Configure your personal access token

Set your github username and token as environment variables. For example, in bash:

```
export GITHUB_USERNAME 'your github username'
export GITHUB_TOKEN 'your github token'
```

Add the repository to your `build.gradle` file as:

```groovy


repositories {
    maven {
        name = "MBARI"
        url = uri("https://maven.pkg.github.com/mbari-org/maven")
        credentials {
            username = project.findProperty("gpr.user") ?: System.getenv("GITHUB_USERNAME")
            password = project.findProperty("gpr.key") ?: System.getenv("GITHUB_TOKEN")
        }
    }
}


```


### SBT

There is an [sbt-github-packages] plugin. This still needs to be tested. Notes about publication can be found [here](https://gist.github.com/djspiewak/2359e5d742cd0d7161cf3422e36fcaf3).

## Developer Usage

First, configure your personal access token as described above.


### Maven

Add the following to your `pom.xml`:

```xml
  <distributionManagement>
    <repository>
      <id>github</id>
      <url>https://maven.pkg.github.com/mbari-org/maven</url>
    </repository>
  </distributionManagement>
```

To publish to GPR run `mvn deploy`

### Gradle

Add the following to your root `build.gradle` in your project:

```groovy
subprojects {

  // ...
  
  apply plugin: 'maven-publish'

  publishing {
      repositories {
         maven {
             name = "GitHubPackages"
             url = uri("https://maven.pkg.github.com/mbari-org/maven")
             credentials {
                 username = project.findProperty("gpr.user") ?: System.getenv("GITHUB_USER")
                 password = project.findProperty("gpr.key") ?: System.getenv("GITHUB_TOKEN")
             }
         }
      }
      publications {
          gpr(MavenPublication) {
              groupId "my.projects.package"
              artifactId project.name
              from components.java
          }
      }
  }
  
}

```
