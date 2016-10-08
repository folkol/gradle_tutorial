# Gradle tutorial


"_I want to get to know you._
_I remember thinking that when i first met you._
_I wanted to get to know everything about you._

_What you look like in the morning,_
_what you look like at night,_
_what you look like, dear Gradle._"

                            [Taylor, hellopoetry.com](http://hellopoetry.com/poem/751072/know-you/)

## References

 - [https://gradle.org/getting-started-gradle-java/]()
 - [https://docs.gradle.org/current/userguide/tutorial_gradle_command_line.html/]()
 - [https://docs.gradle.org/current/userguide/build_init_plugin.html]()
 - [https://docs.gradle.org/current/userguide/build_lifecycle.html]()
 - [https://docs.gradle.org/current/userguide/tutorial_java_projects.html]()
 - [http://docs.groovy-lang.org/latest/html/documentation/index.html]()
 - [https://docs.gradle.org/current/userguide/tutorial_using_tasks.html]()

## Terminology

**Build user:** Someone who needs to build a Gradle user.

**Build author:** Someone who creates Gradle projects.

## Concepts

**Core concepts:**

- Everything in Gradle sits on top of two basic concepts: `projects` and `tasks`.

  - Every Gradle build is made up of one or more `projects`.
  - Every `project` is made up of one or more `tasks`.
  - Every `task` is made up of `Actions`.

**Gradle wrapper:**

Batch file in the root folder of the project that downloads and builds the project using a specific version of Gradle.

The wrapper can be generated with this command:

    $ gradle wrapper --gradle-version 2.0

**Gradle daemon:**

Daemon that is recommended to use on dev machines, will speed up (re-)builds (use --profile to measure this). The Daemon is active by default.

**Project build files:**

    $ grep -r plugin *.gradle
    ...
    apply plugin: "[lang]"
    ...
    plugins { id: "[lang]" }
    ...
    # where: lang in [java, groovy, scala, c, cpp, assembler, objective-c, objective-cpp, android]

**The build life cycle:**

At the core of a Gradle build is a bunch of Groovy scripts that configure and execute a DAG of tasks.

- **Build phases:**

    1. **Initialization:** Find out what projects that will take part of this build and create a `Project` instance for each one.

		`settings.gradle` This file is executed during the initialization phase.

		> **N.b.** Property access and method calls are delegated to the `Settings` object.

		- **Finding the `settings.gradle` file:**

			1. It looks in a directory called master which has the same nesting level as the current dir.
			2. If not found yet, it searches parent directories.
			3. If not found yet, the build is executed as a single project build.
			4. If a `settings.gradle` file is found, Gradle checks _if the current project is part of the multiproject hierarchy_ defined in the found `settings.gradle` file. If not, the build is executed as a single project build. Otherwise a multiproject build is executed.

    2. **Configuration:** Execute build scripts of all `Project`s, to configure them.

		`build.gradle` This file specify tasks, and is executed during the configuration phase.

		> **N.b.** Property access and method calls are delegated to the current `Project` object.

    3. **Execution:** Determine the subset of tasks to be executed, based on command line arguments and the current directory – and execute them in dependency order.

- **The project API and properties**

	- **Gradle build language:**

		1.	Gradle provides a domain specific language, or DSL, for describing builds. This build language is based on Groovy, with some additions to make it easier to describe a build.
		2. A build script can contain any Groovy language element. [6] Gradle assumes that each build script is encoded using UTF-8.
		3. Any method you call in your build script which is not defined in the build script, is delegated to the Project object.

			 `apply plugin: 'java'` will call 'apply()' in the current `Project` instance, unless defined in `build.gradle`.

		4. Any property you access in your build script, which is not defined in the build script, is delegated to the Project object.

	- **Some Groovy basics:**

		1. **Groovy JDK methods:**
			- `configurations.runtime.each { File f -> println f }`
		2. **Property accessors:**
			- `println project.buildDir` == `println getProject().getBuildDir()`
			- `project.buildDir = 'target'` == `getProject().setBuildDir('target')`
		3. **Optional parenthesis:**
		   - `test.systemProperty 'some.prop', 'value'` == `test.systemProperty('some.prop', 'value')`
		4. **List and Map literals:**
		   - `test.includes = ['org/gradle/api/**', 'org/gradle/internal/**']`
		   - `Map<String, String> map = [key1:'value1', key2: 'value2']`
		5. **Named parameters:**
		   - `apply plugin: 'java'` Name parameters are coerced into a map before invocation.
		6. **Closures:**
		   - `{ [closureParameters -> ] statements }` Groovy closures may contain free variables.
		     - `{ item++ }`
		     - `{ -> item++ }`
		     - `{ println it }`
		     - `{ it -> println it }`
		     - `{ String x, int y -> println "hey ${x} the value is ${y}" }`
		   - When the last parameter of a method call is a closure, it may be placed after the method call.
		     - `repositories { println "in a closure" }`
		     - `repositories() { println "in a closure" }`
		     - `repositories({ println "in a closure" })`
		   - Every closure has got a `delegate`, which is used to look up variables and methods which are not local or parameters.
		     - `dependencies { assert delegate == project.dependencies }`
		7. **Default imports:**
			- `import org.gradle.*` and subpackages are imported by default.

- **Tasks:**

   - **Hello World:**

     ```
     $ cat build.gradle
     task hello {
         doLast {
             println 'Hello world!'
         }
     }
     $ gradle hello -q
     Hello world!
     ```

   - **Short form:**

     ```
     task hello << {
         println 'Hello world!'
	  }
	  ```
	  - `<<` means 'evaluate the expression to the right and append to the collection to the left'.

	- **Task dependencies:**

	  ```
     task taskX(dependsOn: 'taskY') << {
	      println 'taskX'
 	  }
     task taskY << {
         println 'taskY'
     }
     ```

     - Note that order is not important.

	- **Creating dynamic tasks:**

     It is the invocation of the task method that creates the task.

		```
		4.times { counter ->
			task "task$counter" << {
				println "I'm task number $counter"
				}
			}
		task0.dependsOn task2, task3
		```

	- **Manipulating tasks with API:**

	   ```
	   task hello << {
		    println 'Hello Earth'
		}
		hello.doFirst {
		    println 'Hello Venus'
		}
		hello.doLast {
		    println 'Hello Mars'
		}
		hello << {
		    println 'Hello Jupiter'
		}
		```

	- **Ant tasks:**

	  - ANT targets are available as `ant.loadfile`.

	- **Default tasks:**

	  - `defaultTasks 'clean', 'run'`

	  - If a subproject does not specify default tasks, the default tasks of the parent project are used (if defined).


- **File handling:**

	- Relative path: `File configFile = file('src/config.xml')`
	- Absolute path: `configFile = file(configFile.absolutePath)`
	- File object with relative path: `configFile = file(new File('src/config.xml'))`
	- Understands URLs, such as  `file:/some/path.xml.`
	- Can take any object, will attempt to convert it to a File object.
	- Can get a file tree: `FileTree tree = fileTree(dir: 'src/main')`
	- Archives: `FileTree zip = zipTree('someFile.zip')` `FileTree tar = tarTree('someFile.tar')`

	- **Copying files:**

		```
		task copyTask(type: Copy) {
			from 'src/main/webapp'
			into 'build/explodedWar'
		}
		```

- **Dependency management:**

	- `compile`, `runtime`, `testCompile` and `testRuntime` dependencies

		```
		apply plugin: 'java'

		repositories {
		    mavenCentral()
		}

		dependencies {
		    compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
		    testCompile group: 'junit', name: 'junit', version: '4.+'
		}
		```


## Commands

**Gradle projects:** Lists projects.

	(master)$ gradle projects
	:projects

	------------------------------------------------------------
	Root project
	------------------------------------------------------------

	Root project 'gradle_tutorial'
	No sub-projects

	To see a list of the tasks of a project, run gradle <project-path>:tasks
	For example, try running gradle :tasks

	BUILD SUCCESSFUL

	Total time: 0.485 secs

**Gradle tasks:** Print information about the tasks that this build can perform.

	$ gradle tasks
	:tasks

	------------------------------------------------------------
	All tasks runnable from root project
	------------------------------------------------------------

	Build Setup tasks
	-----------------
	init - Initializes a new Gradle build. [incubating]
	wrapper - Generates Gradle wrapper files. [incubating]

	Help tasks
	----------
	buildEnvironment - Displays all buildscript dependencies declared in root project 'gradle_tutorial'.
	components - Displays the components produced by root project 'gradle_tutorial'. [incubating]
	dependencies - Displays all dependencies declared in root project 'gradle_tutorial'.
	dependencyInsight - Displays the insight into a specific dependency in root project 'gradle_tutorial'.
	help - Displays a help message.
	model - Displays the configuration model of root project 'gradle_tutorial'. [incubating]
	projects - Displays the sub-projects of root project 'gradle_tutorial'.
	properties - Displays the properties of root project 'gradle_tutorial'.
	tasks - Displays the tasks runnable from root project 'gradle_tutorial'.

	To see all tasks and more detail, run gradle tasks --all

	To see more detail about a task, run gradle help --task <task>

	BUILD SUCCESSFUL

	Total time: 0.509 secs

**Gradle help:** Print help, in this case for the init-task.

	$ gradle help --task init
	:help
	Detailed task information for init

	Path
	     :init

	Type
	     InitBuild (org.gradle.buildinit.tasks.InitBuild)

	Options
	     --type     Set type of build to create.
	                Available values are:
	                     basic
	                     groovy-library
	                     java-library
	                     pom
	                     scala-library

	     --test-framework     Set alternative test framework to be used.
	                          Available values are:
	                               spock
	                               testng

	Description
	     Initializes a new Gradle build. [incubating]

	Group
	     Build Setup

	BUILD SUCCESSFUL

**Gradle init:** Initializes a new gradle project.

	$ gradle init --type=java-library
	:wrapper
	:init

	BUILD SUCCESSFUL

	Total time: 0.546 secs
