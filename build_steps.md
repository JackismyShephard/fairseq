# Build Steps

## Linux

On linux this project must be built in a special manylinux docker container to support the manylinux specification which PyPi requires. This is basically just a specification that ensures that the built wheel will be compatible with as many linux distros as possible by building the project on a linux distro that is sufficiently old (more specifically is using a version of `glibc` which is sufficiently low). Currently (2024) the oldest recommended distro is defined by the platform tag `manylinux_2_28` and the correspoding docker image is `quay.io/pypa/manylinux_2_28_x86_64`. The followings steps define how to build a manylinux compliant wheel for this package.

1. save docker image path to `DOCKER_IMAGE` environment variable and save corresponding platform tag to `PLAT` environment variable.
2. run the command: `docker run -it -v $(pwd):/io $DOCKER_IMAGE` to mount this project in the given manylinux docker container. Make sure that the remaining commands are executed in a shell within the docker container
3. `cd io`
4. make a virtual environment with desired python version (3.12 currently): `python3.12 -m venv .venv`
5. activate it: `. .venv/bin/activate`
6. build source and wheels: `python setup.py sdist bdist_wheel`
7. save the path to the built wheel in `WHEEL_FILE`
8. convert to manylinux spec: `auditwheel repair dist/$WHEEL_FILE -w dist/ --plat $PLAT --only-plat --exclude libtorch_cpu.so --exclude libc10.so --exclude libtorch_python.so --exclude libtorch.so`
9. upload to pypi: `twine upload dist/$WHEEL_FILE`.
10. enter API token when prompted (can be copied but output is invisible)