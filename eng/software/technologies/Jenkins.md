# Jenkins
- CI/CD tool
	- CI: build automation, code integration, test execution
	- CD: integration with configuration management tools, deployment automation
- allows you to write pipeline as code
	- defined using a Groovy-based DSL
## Pipelines
- define the sequence of steps in your software delivery process, from compilation to testing to deployment
- defined as Groovy code in Jenkinsfiles
## Master
- Responsible for:
	- Scheduling builds
	- Distributing workload to agents
	- Storing configuration and build history
	- Providing the UI for managing the system
## Agents
- physical or virtual machines that execute the steps of your pipeline
- has a workspace where Jenkins can store: source code, files generated during build process, etc
- can be static (dedicated machine connected to master) or dynamic (cloud-hosted, provisioned on-demand)
## Build
- a single run of your pipeline
- triggered manually, by a code change (e.g.: triggered via Github webhook), or on a schedule (cron)
- produce artifacts (e.g.: compiled code, packages), test results and logs
- a history is kept of each build, allowing you to track changes, investigate issues and potentially roll back
## Jobs
- overarching container for your entire CI/CD process
- where you configure all the details of a specific build or automation task in Jenkins
- includes: Source code repository, Build triggers, Steps in the pipeline, Notifications
## Plugins
- vast plugin ecosystem that extends Jenkin's core functionality
- enables Jenkins to integrate with tools commonly used in DevOps
## Jenkinsfile
- can do things like:
	- create Docker images using your DockerFile
	- deploy to Kubernetes clusters
	- push to artifactory
	- send notifications / emails after a stage is complete
- utilize parallel stages for faster execution
- can mix and match declarative and scripted syntax
### Declarative Pipeline Syntax
- simpler, less flexible than scripted pipeline syntax
- can embed groovy code with `script`
- `stage`: logical grouping of steps that represent a part of your process e.g.: build/test/stage
- `steps`: list of steps to be executed within the stage
- `post`: section that executes after main pipeline, regardless of success or failure
### Scripted Pipeline Syntax
- more flexible, more complex
- more traditional groovy programming
	- allows for complex logic, conditional branching, loops, and interaction with external systems if needed
## Pipeline Shared Libraries
- reusable Groovy code for your pipelines
- functions and classes (Global Variables), callable from your Jenkinsfiles
	- allows you to standardize common tasks, reuse code
- e.g.: `sendBuildSuccessEmail()`, deployment templates, reusable test stages
- loaded at runtime

## Workflow examples
### Jenkins + Docker
1. **Code Change:** A developer pushes code to the repository.
2. **Jenkins Triggered:** This triggers a Jenkins job or pipeline.
3. **Build Image:** Jenkins pulls your Dockerfile and executes `docker build` creating a new application image.
4. **Run Tests:** Jenkins spins up a Docker container based on that image and executes your test suite inside it.
5. **Push to Registry:** If tests pass, Jenkins can push the image to a Docker registry (Docker Hub, Artifactory, etc.)
6. **Deploy:** Jenkins can interact with tools like Kubernetes to update deployments with the new image.