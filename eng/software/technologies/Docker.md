## Docker
### Benefits
- **Portability** 
	- Docker containers run consistently across different environments (development, testing, production) because they bundle everything needed
		- Note: For a docker container to run on Windows/macOS, Docker runs a Linux VM in order to use the linux kernel, introducing a slight performance overhead
- **Isolation**
	- Containers are isolated from each other and the host system, preventing conflicts and ensuring consistent behavior
- **Lightweight**
	- Containers share the host kernel, making them much faster to start and stop compared to virtual machines
- **Scalability** 
	- Easily scale applications by starting additional containers
- **Reproducibility** 
	- Define your application's environment in a Dockerfile, ensuring everyone gets the same setup
### Images
- Dockerfile provides the blueprint for defining your image
- immutable read-only template
- thought of as a "factory" that produces instances of an image -> container
- built layer-by-layer
	- changes made to a container of an image create a new layer on top of existing image layers
	- optimizing these layers can save space and time in spinning up containers
### Containers
- an ephemeral instance of an image
- bundles everything your software needs to run: source code, external or internal libraries, dependencies, runtime environment, system tools
- runs separate from host OS which prevents conflicts with dependencies or configuration on host that would cause app to behave differently
- daemon adds a thin writable layer where changes made to container are stored
- to create new image from a container:
	- `docker commit <container-id> image-name:tag` 
#### Isolation
- Docker achieves isolation from the host OS by utilizing the linux kernel, namely, namespaces and cgroups
	- this makes containers lightweight
##### Namespaces
- Docker uses namespaces to give containers their own view of system resources
- **PID Namespace**
	- Each container gets its own process ID space, so it sees processes as if it were the only thing running on the system
- **Network Namespace** 
	- Containers have their own network interfaces and IP addresses, preventing conflicts with the host or other containers
- **Mount Namespace**
	- Each container gets its own view of the filesystem. Changes in the container don't affect the host file system
##### cgroups (Control Groups)
- allow the Docker daemon to limit and allocate resources to containers
- **CPU** 
	- Assign CPU time shares, so containers don't hog all CPU cycles of the host
- **Memory** 
	- Set memory limits, preventing a single container from using up all the host's RAM
- **IO** 
	- Throttle disk and network I/O for fair resource distribution
- Note:
	- no default limit to how much disk space a container can use, can set limit on through overlay2 and on size of volumes
### Architecture
- relies on host OS kernel namespace and cgroups which makes them lightweight compared to VMs and to provide isolation between other processes: including separate networking, storage...
- client-server architecture where client sends API calls to daemon
#### Docker Engine
- contains daemon and client
#### Docker Daemon
- background process that manages the building, running and distribution of containers
- workhorse that builds images, creates containers from them and handles storage and networking
- can be remote
#### Docker Client
- UI to interact with daemon
- allows you to send commands to build, run, stop and manage containers and images
##### commands
- Download image from registry: `docker pull <image>`
- Push image to registry: `docker push <registry>/<image>`
- List local images: `docker images`
- Build container: `docker build -t <image> <path-to-dockerfile>`
- Build history of image (useful for optimization): `docker history <image>`
- Running containers: `docker ps`
- Start/stop/restart/kill/remove: `docker start/stop/restart/kill/rm <container>`
- Container info: `docker inspect <container>`
- Container logs: `docker logs <container>`
- Bash shell inside container: `docker exec -it <container> /bin/bash`
- Copying files: `docker cp /path/on/host <container-name>:/path/inside/container`
- Create network: `docker network create <name>`
- Create volume: `docker volume create <name>`
- `docker run` flags:
	- -d = detached, -it = shell
	- -v host-dir:container-dir = mount host dir into container
	- -m: max memory allowed in bytes
	- --cpus: number of cpu cores
### Volumes
- mechanism for persistent data storage outside of containers
#### Bind Mounts
- mount a specific directory on the host machine to the container
- any change made within the directory through the host or container are immediately reflected in both places
- less portable, tied to the host
- useful for setting up a dev environment
- e.g.: `docker run -v /host:/container name` or `docker volume create -d local -o mountpoint=/path/on/host`
#### Named Volumes
- created using `docker volume create <name>` and then run the container with -v flag
- Docker allocates space on the host filesystem
- generally data changes only occur through the container?
- data is transferable between different Docker containers
- useful for storing data that needs to outlive individual containers
### Network
- Docker allows you to create custom networks for your containers to communicate with each other
- by default when you start a container, not exposed to external network requests
	- need to open and map a port on your local machine to the container port via `-p local:host` flag
- custom networks useful for:
	- enhanced security, name-based communication, overlapping IP addresses
### Network Drivers
- Bridge
	- default
	- virtual network with your cost, containers on this network can communicate with each other
	- used for single-host apps
- Overlay
	- containers on different hosts can communicate
	- used for multi-host apps / distributed apps across clusters
- Host
	- use the host network
- None
	- disable networking
### Dockerfile
- contains instructions for daemon on how to build a Docker image
- specifies the base image, what needs to be installed/copied, env variables and commands/scripts to run when the container starts
- builds image layer-by-layer
- if an instruction doesn't change a layer, Docker reuses existing layer, making builds faster
#### Dockerfile Directives
- FROM: set a base image
- WORKDIR: Sets the working directory for subsequent instructions within your container
- RUN: Executes shell commands 
- EXPOSE: open port
- CMD: command to execute when container starts from this image
- ENTRYPOINT
	- can use script to run at container startup
	- with CMD can use to make container behave like a executable
#### Dockerfile Optimization
- smaller image sizes and fast build times are important when you containers are constantly getting built
##### Multi-stage Builds
- Traditional Dockerfiles often end up with final images containing build tools, compilers, and other dependencies needed during the build process, but not crucial for running the application itself, **making images unnecessarily large**
- Multi-stage builds allow you to define multiple stages (separated by `FROM` commands) within a single Dockerfile
	- Each stage can use a different base image and have its own instructions
- Final stage copies only the necessary artifacts from previous stages
- Improves Dockerfile readability and maintainability
- Resulting image is built from the last stage, unless using the -target flag
##### Layered Caching
- speeds up build time
- When building an image, Docker creates a layer for each instruction.
- If the instruction doesn't change, Docker wisely reuses layers from previous builds instead of rebuilding them.
- Organize your Dockerfile in a way that maximizes layer reuse
    - Stable layers first
	    - Place instructions that change infrequently (like OS updates) at the top of your Dockerfile
    - Volatile layers later
	    - Instructions that change more often (like code changes) should come later
### Docker Compose
- container orchestration tool for running multi-container applications
- contains a `docker-compose.yml` file for describing the services/containers that make up your applications and how they connect
- allows you to start and stop all of your containers in a single command via `docker-compose up`
- easier then Kubernetes, but not suitable for production
	- lacks scalability (auto-scaling, load balancing), fault tolerance features (self-healing), service discovery
	- focus is on single-host
- automatically creates a bridge network for all your defined containers
