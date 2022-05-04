# Swift Arm Community CI Server

## The Hardware
The server is the powerful Ampere® Altra® with an 80-core processor, 256GB of RAM and 1.9GB of fast NVMe storage. [More details...](https://solutions.amperecomputing.com/systems/equinix/bare-metal/bare-metal/c3-large-arm64)  
The server is kindly provided by [Equinox](https://metal.equinix.com/) through the [Works On Arm](https://community.arm.com/p/works-on-arm) project.

## CI Software
The server is running [Jenkins](https://www.jenkins.io/doc/) and uses `Jenkins pipelines` for the build jobs.

This repository contains Jenkins pipelines for the Swift Arm Community CI Server build jobs.  
The repository branches contain the Jenkinsfile and patches needed for each build job.

Build jobs, results and downloads can be found at the the `Jenkins` dashboard url.  
https://ci.swiftlang.xyz

## Build Environment
We are using [Docker](https://www.docker.com/) containers for the `Swift` build environments.  
This enables us to build for a variety of different distributions and versions all on the same build host.  
Docker images for all the different build environments can be found at [Docker Hub](https://hub.docker.com/repository/docker/swiftarm/ci-build)

## Community Projects
The server is available for any community Swift on Arm project and new projects are more than welcome.  
Current projects running on the server are -
#### [Swift on Arm64 - Debian/Ubuntu](swift-on-arm64/README.md)
#### [Swift on Armv7 - Debian/Ubuntu](swift-on-armv7/README.md)

## Getting Involved
You can get involved by joining one of the existing projects or creating a new one.  
Github `issues` and `discussions` are open so feel free to use them.  
Posting on the [swift.org forums](https://forums.swift.org/) is also a good place to discuss Swift on Arm projects.
