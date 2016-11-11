# gradle-execfork-plugin

Gradle plugin for running background processes during a build. Both standard executables and java classes are supported.

## Usage
For running a standard executable:

```groovy
apply plugin: 'gradle-execfork-plugin'

buildscript {
  dependencies {
    classpath 'com.github.psxpaul:gradle-execfork-plugin:0.1.4'
  }
}

task startDaemon(type: com.github.psxpaul.task.ExecFork) {
    commandLine = './MainScript.sh'
    args = [ '-d', '/foo/bar/data', '-v', '-l', '3' ]
    workingDir = "$projectDir/src/main/bash"
    standardOutput = "$buildDir/daemon.log"
    errorOutput = "$buildDir/daemon-error.log"
    stopAfter = verify
    waitForPort = 8080
}
```

For running a java main class:

```groovy
apply plugin: 'gradle-execfork-plugin'

buildscript {
  dependencies {
    classpath 'com.github.psxpaul:gradle-execfork-plugin:0.1.4'
  }
}

task startDaemon(type: com.github.psxpaul.task.JavaExecFork) {
    classpath = sourceSets.main.runtimeClasspath
    args = [ '-d', '/foo/bar/data', '-v', '-l', '3' ]
    jvmArgs = [ '-Xmx500m', '-Djava.awt.headless=true' ]
    workingDir = "$buildDir/server"
    standardOutput = "$buildDir/daemon.log"
    errorOutput = "$buildDir/daemon-error.log"
    stopAfter = verify
    waitForPort = 8443
}
```

### Supported Properties
ExecFork:
Name | Type | Description
--- | --- | ---
workingDir | String | *Optional.* The path of the working directory to run the executable from. This is treated as a relative path, so specifying an absolute path should be preferred. Default: `project.projectDir.absolutePath`
args | List<String> | *Optional.* A list of arguments to give to the executable.
standardOutput | String | *Optional.* The path of the file to write standard output to. If none is specified, process output is written to gradle's console output.
errorOutput | String | *Optional.* The path of the file to write error output to. If none is specified, the error output is directed to the same destination as the standard output.
waitForPort | Int | *Optional.* A port number to watch for to be open. Until opened, the task will block. If none is specified, the task will return immediately after launching the process.
timeout | Long | *Optional.* The maximum number of seconds associated with the waitForPort. Default: `60`
stopAfter | org.gradle.api.Task | *Optional.* A task that, when finished, will cause the process to stop. If none is specified, the process will stop at the very end of a build (whether successful or not).
commandLine | String | *Required.* The path to the executable.

JavaExecFork:
Name | Type | Description
--- | --- | ---
workingDir | String | *Optional.* The path of the working directory to run the executable from. This is treated as a relative path, so specifying an absolute path should be preferred. Default: `project.projectDir.absolutePath`
args | List<String> | *Optional.* A list of arguments to give to the executable.
standardOutput | String | *Optional.* The path of the file to write standard output to. If none is specified, process output is written to gradle's console output.
errorOutput | String | *Optional.* The path of the file to write error output to. If none is specified, the error output is directed to the same destination as the standard output.
waitForPort | Int | *Optional.* A port number to watch for to be open. Until opened, the task will block. If none is specified, the task will return immediately after launching the process.
timeout | Long | *Optional.* The maximum number of seconds associated with the waitForPort. Default: `60`
stopAfter | org.gradle.api.Task | *Optional.* A task that, when finished, will cause the process to stop. If none is specified, the process will stop at the very end of a build (whether successful or not).
classpath | org.gradle.api.file.FileCollection | *Required.* The classpath to use to launch the java main class.
main | String | *Required.* The qualified name of the main java class to execute.
jvmArgs | List<String> | *Optional.* The list of arguments to give to the jvm when launching the java main class.

#TODO:
 - add a waitForOutput option
