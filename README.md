# docker-r

This repository contains a collection of templated `Dockerfile` for image variants designed

## Usage

### Template Variables

- `R_BASE_VERSION` - Version number for R
- `MRAN_SNAPSHOT_DATE` - Snapshot date for MRAN

### Testing

An example of how to use `cibuild` to build and test an image:

```bash
$ CI=1 R_BASE_VERSION=3.6.2 MRAN_SNAPSHOT_DATE=2020-04-01 \
  ./scripts/cibuild
```
