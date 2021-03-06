---
title:  "Kikaha: an awesome web server container for Java haters"
date:   2014-10-23 01:34:00
categories: java web-server kikaha
---

I don't work in the development industry for that long, but I know people hate Java. They point out a lot of reasons: slow, verbose, cumbersome. 

Yet, [TechEmpower benchmarks](http://www.techempower.com/benchmarks/) put a lot of Java platforms and frameworks as some of the fastest in the market, with some others that rely on JVM itself (like Scala).

Also, [Project Lombok](http://projectlombok.org/) exists to help developers reduce the amount of code they write, providing a set of annotations that are resolved during compile time to reduce repetitive tasks (getters and setters generation, anyone?).

The only coherent argument is the cumbersomeness. Java still supports outdated features that could easily be put in external packages, but with so many enterprise applications running and the urge to update systems I doubt Oracle will address that soon.

With so many features developed by so many awesome people, my pal [Miere](https://twitter.com/miere) with the guys at [Texo IT](http://www.texoit.com/) decided to create a platform to solve most of the problems with web development in Java.

I never programmed in Java professionally, but I'm so happy with the results they are achieving that I wanted to do something with it. I have some ideas, but, to start, I need to learn how to set the environment and to run the first applications. So, it's Hello World time!

I talked with Miere about the fastest setup I can come with, and decided the basic Eclipse approach. Installed the plugins Maven Integration Plugin and m2e-apt from the Eclipse Marketplace (Help > Eclipse Marketplace...) and created a new Maven project. After that, we should have a ```pom.xml``` like this:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>kikaha</groupId>
	<artifactId>HelloWorld</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>Kikaha Hello World</name>
</project>
```

This was easy. :D

Kikaha works with the ```maven install``` command (for reasons that I'm not sure why, but will dig further into the issue for sure), so we'll need to configure that. To do it, let's add some build plugins:

```xml
<project xmlns="...">
	<!-- snip -->
	<build>
		<plugins>
			<plugin>
				<groupId>io.skullabs.kikaha</groupId>
				<artifactId>kikaha-maven-plugin</artifactId>
				<version>${version.kikaha}</version>
			</plugin>
			<plugin>
				<inherited>true</inherited>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>${version.maven.plugin}</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
					<optimize>false</optimize>
					<debug>true</debug>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

These two will deal with the command line tools.

```xml
<project xmlns="...">
	<!-- snip -->
	<dependencies>
		<!-- Compile time dependencies -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>${version.lombok}</version>
			<scope>provided</scope>
		</dependency>
		<!-- Runtime dependencies -->
		<dependency>
			<groupId>io.skullabs.kikaha</groupId>
			<artifactId>kikaha-core</artifactId>
			<version>${version.kikaha}</version>
		</dependency>
	</dependencies>
</project>
```

From top to bottom, what are these:
* Lombok: the "annotations helper".
* Kikaha: THE guy.

Sad thing is Kikaha is not in default maven repositories (yet!). So, we'll have to add them manually.

```xml
<project xmlns="...">
	<!-- snip -->
	<repositories>
		<repository>
			<id>skullabs-release-repository</id>
			<name>Skullabs Release Repository</name>
			<url>http://skullabs.io:8081/content/repositories/PublicRelease/</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
		<repository>
			<id>skullabs-snapshot-repository</id>
			<name>Skullabs Snapshot Repository</name>
			<url>http://skullabs.io:8081/content/repositories/PublicSnapshot/</url>
			<releases>
				<enabled>false</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
			<id>skullabs-release-repository</id>
			<name>Skullabs Release Repository</name>
			<url>http://skullabs.io:8081/content/repositories/PublicRelease/</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
		<pluginRepository>
			<id>skullabs-snapshot-repository</id>
			<name>Skullabs Snapshot Repository</name>
			<url>http://skullabs.io:8081/content/repositories/PublicSnapshot/</url>
			<releases>
				<enabled>false</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</pluginRepository>
	</pluginRepositories>
</project>
```

Yeah, XMLs get huge. [Here's the final version](https://gist.github.com/msilvagarcia/69a81db015777ddd6ccf).

With this we can run Kikaha on its own, but it won't be very useful. So let's add a web page to see something.

Create the folder ```webapp``` inside  ```$PROJECT_ROOT/src/main```, then create a ```hello.html``` file inside it. Make it as simple as:

```html
<html>

<head>
	<title>Hello world!</title>
</head>

<body>
	<h1>Hello world!</h1>
</body>

</html>
```

Now, time to rock! Create a new run configuration and set the following:

* Name: give a name to this configuration, like ```kikaha-hello-world```.
* Base directory: your project root, you can select one by clicking in "Browse Workspace..." and choosing the project you created.
* Goals: type ```clean install kikaha:run``` inside.

Apply and run.

The Eclipse console should write lots of things, but the last line should say where Kikaha is listening to connections, like:

```
Oct 22, 2014 21:33:09 PM kikaha.core.UndertowServer start
INFO: Server is listening at 0.0.0.0:9000
```

Go to your browser and access [http://0.0.0.0:9000/hello.html](http://0.0.0.0:9000/hello.html) and see the magic happening.

Static files can be updated while Kikaha is running, so you can check how your site is going while you are building it. Of course we have lot's of solutions for static file deployment, but from what I've seen, Kikaha can make web development in Java look cool.

The full code for the project can be found in [this github page](https://github.com/msilvagarcia/KikahaHelloWorld).
