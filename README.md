# How to Create a Java Dependency and Upload it to Maven 📝  

# Table of contents  
1. [Introduction](#introduction)  
2. [Get Started](#get-started-)
3. [Step 1: Create a Java Project](#step-1-create-a-java-project)
4. [Step 2: Initial Setup (Setting Jira & GitHub)](#step-2-initial-setup-of-jira-and-github)
5. [Step 3: Add the following in your pom.xml](#step-3-add-the-following-in-your-pomxml) 
6. [Step 4: Add the following in your setting.xml](#step-4-add-the-following-in-your-settingxml) 
7. [Step 5: Uploading Process / Upload to Maven](#step-5-uploading-process--upload-to-maven)

## Introduction
Creating a Java dependency and uploading it to Maven is an essential task for Java developers. It allows other developers to use your code in their projects without having to copy and paste it. In this readme file, we will discuss how to create a Java dependency and upload it to Maven. You can also create a dependency that can be used in you organization only using Nexus Repository Manager. 

#### **Sample dependency that validates Zimbabwean Phone numbers found [here](https://github.com/AnesuPhiri521/zimbabwean-phone-number-validator). Take a look at the pom.xml**

## Get Started 🚀  
Before we begin, make sure you have the following installed:
- Java Development Kit (JDK) version 8 or higher
- Apache Maven
- GPG installed
- Hosting server in this case I'm using GitHub

## Step 1 Create a Java Project
To create a Java project, follow these steps:
1. Open your terminal or command prompt.
2. Navigate to the directory where you want to create your project.
3. Run the following command: 

```mvn archetype:generate -DgroupId=com.example -DartifactId=my-project -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false```

This will create a new directory called `my-project` with a basic Java project structure.
  
Add your code to the project/develop your dependency.

## Step 2 Initial Setup of Jira and GitHub
1. Create a GitHub repository and make it public
2. Create Jira account [here](https://issues.sonatype.org/secure/Signup!default.jspa)
3. Create Project ticket [here](https://issues.sonatype.org/secure/CreateIssue.jspa?pid=10134&issuetype=21)
4. Verify your GitHub account by following the steps of creating a temporary repository



## Step 3 Add the following in your `pom.xml`

Distribution Management and Authentication:

In order to configure Maven to deploy to the OSSRH Nexus Repository Manager with the Nexus Staging Maven plugin you have to configure it like this

```
<distributionManagement>
  <snapshotRepository>
    <id>ossrh</id>
    <url>https://s01.oss.sonatype.org/content/repositories/snapshots</url>
  </snapshotRepository>
</distributionManagement>
<build>
  <plugins>
    <plugin>
      <groupId>org.sonatype.plugins</groupId>
      <artifactId>nexus-staging-maven-plugin</artifactId>
      <version>1.6.7</version>
      <extensions>true</extensions>
      <configuration>
        <serverId>ossrh</serverId>
        <nexusUrl>https://s01.oss.sonatype.org/</nexusUrl>
        <autoReleaseAfterClose>true</autoReleaseAfterClose>
      </configuration>
    </plugin>
  </plugins>
</build>
```


Javadoc and Sources Attachments

To get Javadoc and Source jar files generated, you have to configure the Javadoc and source Maven plugins.
```
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-source-plugin</artifactId>
      <version>2.2.1</version>
      <executions>
        <execution>
          <id>attach-sources</id>
          <goals>
            <goal>jar-no-fork</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-javadoc-plugin</artifactId>
      <version>2.9.1</version>
      <executions>
        <execution>
          <id>attach-javadocs</id>
          <goals>
            <goal>jar</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

GPG Signed Components
The Maven GPG plugin is used to sign the components with the following configuration.
```
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-gpg-plugin</artifactId>
      <version>1.5</version>
      <executions>
        <execution>
          <id>sign-artifacts</id>
          <phase>verify</phase>
          <goals>
            <goal>sign</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```



## Step 4 Add the following in your `setting.xml`

The above configurations will get the user account details to deploy to OSSRH from your Maven settings.xml file, usually placed in ~/.m2. A minimal settings with the authentication is:

I got stuck to get these, don't use your Jira username and password; e.g. if your password has special characters the settings.xml file won't compile.  

#### **NB: Navigate to [Nexus Repository](https://s01.oss.sonatype.org/#profile;Summary)**
* Login with your Jira Credentials.
* Navigate to your profile
* On the dropdown default value is a Summary select token
* Copy the values on the generated XML and populate your username and password
* Make sure you leave the id as `ossrh`

```
<settings>
  <servers>
    <server>
      <id>ossrh</id>
      <username>your-jira-id</username>
      <password>your-jira-pwd</password>
    </server>
  </servers>
</settings>
```

In the same `setting.xml` navigate to settings under profile.

* Copy the below `profile` and paste it.
* You only replace `the_pass_phrase`

How to get the `the_pass_phrase`

* Open GPG and generate a public key 
* The password set is the one that will replace `the_pass_phrase`
* Make sure that you upload the key to Ubuntu public server.

It relies on the GPG command being installed and the GPG credentials being available e.g. from settings.xml. In addition, you can configure the GPG command in case it is different from gpg. This is a common scenario on some operating systems.

```
<settings>
  <profiles>
    <profile>
      <id>ossrh</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <properties>
        <gpg.executable>gpg2</gpg.executable>
        <gpg.passphrase>the_pass_phrase</gpg.passphrase>
      </properties>
    </profile>
  </profiles>
</settings>
```

## Step 5 Uploading Process / Upload to Maven

1. Push your files to the GitHub repo you created
2. `mvn clean deploy` 
3. `mvn clean deploy -P release`

Congratulations! You have successfully created a Java dependency and uploaded it to Maven. Other developers can now use your code in their projects by simply adding it as a dependency in their pom.xml files

The dependency will deploy maven. It may take time to appear, but it will be instantly uploaded at [here](https://s01.oss.sonatype.org/)
