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


## Concepts 
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


## Commands

**Gradle projects:**

Lists projects.

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

**Gradle tasks:**

Print information about the tasks that this build can perform.

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

**Gradle help:**

Print help, in this case for the init-task.

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
