# Welcome
Welcome to the official Wiki page for the Clash core project ("Clash"). There are currently two versions of Clash:
* Clash: the software released at [Dreamacro/clash](https://github.com/Dreamacro/clash)
* [Clash Premium](https://github.com/Dreamacro/clash/releases/tag/premium): close-sourced pre-built Clash binary with TUN support (it's free)

This wiki will cover both versions of Clash. 

> This documentation is still in the works, and we strive to improve it.  
  You are welcome to contribute.

# Getting Started
You can either grab the pre-built binaries of Clash from [https://github.com/Dreamacro/clash/releases](https://github.com/Dreamacro/clash/releases) or build locally.  
Clash requires Golang 1.13 or a higher version.

```
$ go get -u -v github.com/Dreamacro/clash
$ GOOS=darwin GOARCH=amd64 go build github.com/Dreamacro/clash
```

No output indicates that the build was successful. You should get the executable binary in your current directory.

```
$ file clash
$ clash -v
```

You can now move forward to the next chapters of this wiki in which we'll cover the configuration syntax of Clash.

# The Developers
There will be no Clash without the following incredible developers:
* [Dreamacro](https://github.com/Dreamacro)
* [beyondkmp](https://github.com/beyondkmp)
* [comzyh](https://github.com/comzyh)
* [kr328](https://github.com/kr328)
* [duament](https://github.com/duament)
* [Fndroid](https://github.com/Fndroid)

You can find the full list of contributors to this repository at [https://github.com/Dreamacro/clash/graphs/contributors](https://github.com/Dreamacro/clash/graphs/contributors).

# License
This software is released under the [GPL-3.0](https://github.com/Dreamacro/clash/blob/master/LICENSE) open-source license.  
Side note: before Oct, '19, Clash was licensed under the MIT license.