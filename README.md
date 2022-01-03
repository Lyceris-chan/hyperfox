## Building LibreWolf from source:

First, let's **[download the latest tarball](https://gitlab.com/librewolf-community/browser/source/-/jobs/artifacts/main/raw/librewolf-95.0.2-2.source.tar.gz?job=build-job)**. This tarball is the latest produced by the CI.

To download the latest from a script, use wget/curl like this:
```
wget -O librewolf-95.0.2-2.source.tar.gz https://gitlab.com/librewolf-community/browser/source/-/jobs/artifacts/main/raw/librewolf-95.0.2-2.source.tar.gz?job=build-job
curl -L -o librewolf-95.0.2-2.source.tar.gz https://gitlab.com/librewolf-community/browser/source/-/jobs/artifacts/main/raw/librewolf-95.0.2-2.source.tar.gz?job=build-job
```

Next, we create ourselves a build folder and extract the tarball.

```
mkdir build
cd build
tar xf ../librewolf-95.0.2-2.source.tar.gz
```
Next step, if you have not done so already, you must create the build environment:
```
librewolf-95.0.2/lw/mozfetch.sh
```
This would create a _mozilla-unified_ folder in our 'build' folder. It takes about an hour for me to complete, but it needs to be done only once. This step might fail and cause problems. Hack a bit, and if that fails you can ask on our [Gitter](https://gitter.im/librewolf-community/librewolf)/[Matrix](https://matrix.to/#/#librewolf:matrix.org) channels. There is no need to actually build _mozilla-unified_ (Mozilla Nightly) itself, nor is the folder needed to build LibreWolf. So you can remove it: `rm -rf mozilla-unfied` if you don't plan on using/exploring it.

Now we're ready to actually build LibreWolf:
```
cd librewolf-95.0.2
./mach build
```
Also takes me an hour. Then, we can run it:
```
./mach run
```
Or make a package:
```
./mach package
```

### I want to keep up to date with the latest, but compile myself

1. To first clone the repo:
```
git clone https://gitlab.com/librewolf-community/browser/source.git
cd source
make librewolf
```
2. To keep up-to-date:
```
git pull
make librewolf
```

## [dev info] How to use this repo instead of [Common](https://gitlab.com/librewolf-community/browser/common):

Since the dawn of time, we have used **Common** to get _patches_, _source_files_, including _source_files/{branding}_

This source repo supports all that, because it uses these same things to produce the tarball. As far as I can tell, the mapping from Common to Source would be:

* _[patches](https://gitlab.com/librewolf-community/browser/common/-/tree/master/patches)_ -> _[patches](https://gitlab.com/librewolf-community/browser/source/-/tree/main/patches)_
* _[source\_files](https://gitlab.com/librewolf-community/browser/common/-/tree/master/source_files)/search-config.json_ -> _[assets](https://gitlab.com/librewolf-community/browser/source/-/tree/main/assets)/search-config.json_
* _source\_files/browser/[branding](https://gitlab.com/librewolf-community/browser/common/-/tree/master/source_files/browser/branding)/librewolf_ -> _themes/browser/[branding](https://gitlab.com/librewolf-community/browser/source/-/tree/main/themes/browser/branding)/librewolf_


With this mapping, I hope that other builders that can't use our tarball (afterMozilla project, weird distro's), still use the same source/patches as the builders that do use it.

### Another feature

The file [assets/patches.txt](https://gitlab.com/librewolf-community/browser/source/-/blob/main/assets/patches.txt) defines what patches go in. These are not the only patches a builder will use, weird distro's etc, will use additional patches. those patches can live in the repo of that distro, or in a subfolder here. I hope this gives everybody the freedom to build anyway they please, like in Common, but with the added benefit that we produce a source tarball.

### Implementing a build script the new way:

The repository has a short [example shell script](https://gitlab.com/librewolf-community/browser/source/-/blob/main/scripts/fetch-build.sh) on how to use the new-style tarball approach instead of the older patching-it-yourself approach.

## [dev info] Building the LibreWolf source tarball:

Luckly, you don't need the build environment for this. If you don't have write access, just:
```
git clone https://gitlab.com/librewolf-community/browser/source.git
cd source
make all
```
If you **do** have write access, we're first gonna check for a newer version of Firefox:
```
git clone git@gitlab.com:librewolf-community/browser/source.git
cd source
make check
```
If there is a new version, it's a good time to git commit and trigger a CI build job.
```
git commit -am v$(cat version)-$(cat release) && git push
```
To build the source archive:
```
make all
```
If you have a working build environment, you can build librewolf with:
```
make librewolf
```
This extracts the source, and then tries to `./mach build && ./mach package`.


## FAQ: Common issues when setting up the Mozilla build environment

1. it doesnt find a suitable python.
```
export MACH_USE_SYSTEM_PYTHON=1
make librewolf
```
2. <python-package-1> requires <python-package-2, which is not installed.
```
pip3 install <python-package=2>
```
And retry.
