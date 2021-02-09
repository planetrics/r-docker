 docker-r

This repository contains a collection of templated `Dockerfile` for R image variants:
* Base images
* RStudio (v1.25) images. These contain an RStudio server layer on top of each base image 

Each variant is pushed to an external Docker image repository (quay.io). *Do not use the Dockerfiles in this repository directly*.

## Usage

When run in CI (Continuous Integration) mode, the images in this repo are tested and pushed to quay.io/vividfinancepg
Builds are triggered when merging to the `main` branch

To use the images, reference the relevant `quay.io/vividfinancepg/r:<R version>-<MRAN snapshot date>` image
 in the `docker run` command, or in the `FROM` line of your Dockerfile. For example (using R 3.6.3 and MRAN from 2020-04-01):

From the command line:

`docker run --rm quay.io/vividfinancepg/r:3.6.3-2020-04-01 r -e "print('Hello world')"`

From a Dockerfile:

```Dockerfile
FROM quay.io/vividfinancepg/r:3.6.3-2020-04-01
CMD ["r", "-e", "print('Hello world')"]
```

### Running RStudio for interactive analysis
Tips:
- Set a memory limit because R is a bit of a memory hog (--memory <some value e.g. "8g">)
- Mount one or more real directories (-v) from your host machine into the container and work from there. If the
  referenced folder doesn't exist, it will be created.
- RStudio server asks for a password (`-e PASSWORD=<some password>`, but this can be disabled (`-e DISABLE_AUTH=true` instead)
- Run in detatched mode (-d) otherwise the container will exit after startup. It will run as continuously unless stopped
- Expose a port of your choice and access R Studio at http://localhost:<port>, username `rstudio` and password as chosen

#### Example usage

Step 1: Define environment variables to use in docker run command - these can also just be inserted directly into the command
```
# Define all the variables upfront (this can be done inline on the docker run command)
rstudio_port=8080
work_dir="$(pwd)"/rstudio_work
mem_limit=9g
rstudio_password=setapassword  # If you want to run with a password
```
Step 2: Run RStudio server

`docker run --rm -it -d -e PASSWORD=$rstudio_password -p $rstudio_port:8787 --memory $mem_limit -v $work_dir:/home/rstudio quay.io/vividfinancepg/r-studio:3.6.2-2020-04-01`

You can now log into http://localhost:8080 and log in with username `rstudio` and password `setapassword`. If you do not want
to use authentication, you can run the container without it:

`docker run --rm  -it -d -e DISABLE_AUTH=true -p $rstudio_port:8787 --memory $mem_limit -v $work_dir:/home/rstudio quay.io/vividfinancepg/r-studio:3.6.2-2020-04-01`
`

### Template Variables

- `R_BASE_VERSION` - Version number for R
- `MRAN_SNAPSHOT_DATE` - Snapshot date for MRAN

### Testing

Test builds by running `scripts/cibuild` for a given R_BASE_VERSION and MRAN_SNAPSHOT_DATE. This builds
a base and RStudio image pair, and then runs a test script that runs the base image, prints a message, and exits

An example of how to use `cibuild` to build and test an image:

```bash
$ CI=1 R_BASE_VERSION=3.6.2 MRAN_SNAPSHOT_DATE=2020-04-01 \
  ./scripts/cibuild
```
