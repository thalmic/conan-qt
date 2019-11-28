# Maintaing this recipe

## Recipe Version Support

| Qt Version    | Upstream Branch   | North Branch |
| ------------- |:----------------- |:-------------|
| 5.12.5        | stable/5.12.5     | north-1     |

## Submitting changes

You can fork the fork of the thalmic conan-qt fork if you want.  But it seems simpler to just push your changes to a a branch in the thalmic fork and PR from there.

> Note: Please delete your branch as part of merging your PR

## Updating Recipes/Qt Version

1. `git remote add upstream git@github.com:bincrafters/conan-qt`

> Add conan-qt as an upstream

2. `git fetch upstream`
3. `git checkout -b north-2 upstream/stable/5.13.2`

> create a new branch from the upstream target branch.  In this example, we want to upgrade to qt 5.13.2

4. `git push origin north-2`

> push the new branch to the remote (NOTE: this is a vanilla branch)

5. `git rebase north-1`

> rebase the old branch with north changes onto the newest upstream branch

> Note: the Qt 5.12.5 branch does not have a common ancestor to mainline developing so any North changes will need to be cherry-picked in `north-2`

6. update the version in conanfile.py to match the branch version

7. Discover and update any external dependencies following the instructions below

## External Dependencies

Building depends on the following external dependencies that must be imported into North's conan-local remote.  When these dependencies change, you'll need to run the import team city jobs to import the updated dependencies below:

| Package Name    | Remote Url   | Package URL | Profile(s) |
| ------------- |:----------------- |:-------------|:-------------|
| openssl/1.1.1d@\_/\_ | https://api.bintray.com/conan/conan/conan-center | https://bintray.com/conan/conan-center/openssl%3A_ | clang+llvm-7-x86_64-linux, osx_10.14_clang_10.0 |
| xkbcommon/0.8.4@bincrafters/stable | https://api.bintray.com/conan/bincrafters/public-conan | https://bintray.com/bincrafters/public-conan/xkbcommon%3Abincrafters | clang+llvm-7-x86_64-linux |
| zlib/1.2.11@\_/\_ | https://api.bintray.com/conan/conan/conan-center  | https://bintray.com/conan/conan-center/zlib%3A_ | clang+llvm-7-x86_64-linux, osx_10.14_clang_10.0 |

### Import team city jobs
For Linux: Run the 'Add external linux package to local repositories' build
For Mac: Run the 'Add external OSX 10.14, Mojave' build

## Building locally

You can build the recipe that same way that team city does by following these instructions.  From our `conan-recipes` github project:

1. `docker build -f Dockerfile-black+coral+qt5+clang+llvm-7-x86_64-linux -t black-coral-qt5-clang-llvm-7-x86_64-linux .`

> Build the dockerfile for the profile in `cd configuration/dockerfiles`

2. `docker run -it -rm -v $PWD:/home/conan-recipes black-coral-qt5-clang-llvm-7-x86_64-linux /bin/bash`

> Run the container and mount the recipes directory

3. `conan remote remove conan-center`

> Removes the default conan remote

4. `conan remote add north-local <url>`

> Add the North remote, see: https://github.com/thalmic/conan-recipes

5. cd /home/conan-recipes/black-coral/ext/conan-qt

6. `conan create --profile=/home/conan-recipes/configuration/profiles_debian/black+coral+qt5+clang+llvm-7-x86_64-linux --keep-source . black-coral/testing`

> Build the qt package with the black coral profile
