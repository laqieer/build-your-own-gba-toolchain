# build-your-own-gba-toolchain
🤓 Build your own toolchain for gba dev

Inspired by [build-your-own-x](https://github.com/danistefanovic/build-your-own-x).

Artifacts are released [here](https://github.com/laqieer/build-your-own-gba-toolchain/releases/latest).

Only for education purpose. Don't use it for non-trivial development.

## Build toolchain

Read [crosstool-NG](https://crosstool-ng.github.io/)'s [doc](https://crosstool-ng.github.io/docs/). The version I use is [1.24.0](https://crosstool-ng.github.io/2019/04/13/release-1.24.0.html).

You can load `.config` file in its config menu. Don't forget to change `Toolchain ID string` and `Toolchain bug URL` in `Toolchain options` pls. Feel free to tweak other options for your own toolchain. You can also load your own patches in `CT_TOP_DIR` if you have.

Here are problems I met and solutions I found:

1. My OS is [CentOS 7](https://www.centos.org/). Accoding to crosstool-NG 1.24.0's [release note](https://crosstool-ng.github.io/2019/04/13/release-1.24.0.html), its g++ version cannot build gdb.
**Solution**: Follow [this doc](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-8/) to update it.

1. OS's setting up. According to crosstool-NG's [doc](https://crosstool-ng.github.io/docs/os-setup/), you should read its dockerfile to figure out its dependencies.
**Solution**: Read dockerfile as it says. For CentOS 7, `yum install -y autoconf gperf bison file flex texinfo help2man gcc-c++ libtool make patch ncurses-devel python36-devel perl-Thread-Queue bzip2 git wget which xz unzip`.

1. `[ERROR]  You must NOT be root to run crosstool-NG.`
**Solution**: Run it as another user or `CT_ALLOW_BUILD_AS_ROOT=y` according to [this](https://stackoverflow.com/questions/17466017/how-to-solve-you-must-not-be-root-to-run-crosstool-ng-when-using-ct-ng).

1. `[ERROR]  Don't set LD_LIBRARY_PATH. It screws up the build.`
**Solution**: `unset LD_LIBRARY_PATH` accoding to this [doc](https://github.com/jcmvbkbc/crosstool-NG/blob/069dfea0c7372b76a40d6a67152c8a285df0f74c/docs/B%20-%20Known%20issues.txt#L255).

To sum up, [Google Is Your Best Friend](http://giybf.com/).

## Test toochain

Now we have our own toolchain, let's test it.

All files in `test` directory are borrowed from somewhere else (`first.c` from [tonc](https://www.coranac.com/tonc/text/toc.htm) and others from [here](https://github.com/felixjones/gba-toolchain) with a little modifications). I know the code is ugly, but it is only for test.

```
arm-gba-eabi-gcc -nostartfiles -Xlinker -T gba.ld first.c crt0.s gba-syscalls.c
arm-gba-eabi-objcopy -O binary a.out first.gba
```

Then we get `first.gba` and test it. It works fine. Don't forget to [gbafix](https://gbadev.org/tools.php?showinfo=76) it for release.

Here are some notes:

1. This is only to test toolchain. For non-trivial development, use [build automation](https://en.wikipedia.org/wiki/List_of_build_automation_software).

1. `crt0` and `stub` can also be merged into libc which is a part of our toolchain. Follow this [guide](https://www.embecosm.com/appnotes/ean9/ean9-howto-newlib-1.0.html) for [newlib](https://sourceware.org/newlib/).

1. `gba.ld` here is [linker script](https://ftp.gnu.org/old-gnu/Manuals/ld-2.9.1/html_chapter/ld_3.html) for gba cartridge. Use [this](https://github.com/felixjones/gba-toolchain/blob/master/lib/multiboot/gba.ld) for multiboot.

