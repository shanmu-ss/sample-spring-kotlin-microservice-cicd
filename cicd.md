# CICD piple in Tekton 

> Sandbox Created [space link](https://console-openshift-console.apps.sandbox-m2.ll9k.p1.openshiftapps.com)

---

## Pipeline: sample-java-pipeline

This Tekton Pipeline, named `sample-java-pipeline`, is defined in the `tinlam2-dev` namespace. It consists of a sequence of tasks that automate various steps in the software development lifecycle.


### Specification

#### Tasks

The pipeline comprises several tasks, each performing a specific action.

1. **Task: git-clone**
   - **Description**: Clones a Git repository.
   - **Parameters**:
     - `url`: Git repository URL (https://github.com/shanmu-ss/sample-spring-kotlin-microservice-cicd.git)
     - `revision`: Git branch/revision (master)
   - **Task Reference**: ClusterTask named `git-clone`
   - **Workspaces**:
     - `output` mapped to `source-dir`

2. **Task: get-version**
   - **Description**: Retrieves the project version using Maven.
   - **Runs After**: `git-clone`
   - **Task Reference**: Task named `maven-get-project-version`
   - **Workspaces**:
     - `source` mapped to `source-dir`

3. **Task: run-application**
   - **Description**: Builds and runs the application using Maven.
   - **Runs After**: `get-version`
   - **Task Reference**: Task named `maven-build-task`
   - **Workspaces**:
     - `source` mapped to `source-dir`

4. **Task: build-and-push-image**
   - **Description**: Builds and pushes a Docker image to a container registry.
   - **Parameters**:
     - `IMAGE`: Docker image repository and tag.
   - **Runs After**: `run-application`
   - **Task Reference**: ClusterTask named `jib-maven`
   - **Workspaces**:
     - `source` mapped to `source-dir`

#### Task: maven-build-task

This task is responsible for building a Maven project.

- **Steps**:
  - Runs the `mvn clean` command using the Maven image.
- **Image**: Maven 3.8.3 with OpenJDK 17.
- **Working Directory**: The workspace path specified by `$(workspaces.source.path)`.

#### Workspaces

- **source-dir**: Workspace for storing source code and build artifacts.
- **sonar-settings**: Workspace for SonarQube settings.

### Pipeline Execution Flow

1. **git-clone**: Clones the Git repository.
2. **get-version**: Retrieves the project version using Maven (runs after git-clone).
3. **run-application**: Builds and runs the application using Maven (runs after get-version).
4. **maven-build-task**: Builds the Maven project (runs after run-application).
5. **build-and-push-image**: Builds and pushes the Docker image to the specified repository (runs after maven-build-task).

---

The `maven-build-task` task is an essential step in the pipeline, responsible for building the Maven project using the specified Maven version and working directory. It's a crucial part of the pipeline's overall flow, ensuring proper project compilation and preparation for subsequent steps. Feel free to customize the Maven command or image as needed for your specific build requirements.
