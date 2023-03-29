# Installing emergent
![simulationexample](https://i.imgur.com/Yjof8ac.png)
The current process for setting up the new emergent is distributed across several webpages and can be a bit confusing or even contradictory. This is an attempt to put it all in one place, potentially into simpler terms, and with troubleshooting tips informed by my own experience getting everything running.
### File structure:
You need to set the `GOPATH` and `GOROOT`.

* `GOROOT` is where the go executable is
* `GOPATH` is where to install go packages.

You'll use `GOPATH` for the actual project folders for your model. Each project will have a directory, so it'll be something like `GOPATH/src/github.com/emer/leabra/examples/PROJECTNAME.`

Note that `go` forces the separation of `GOPATH` and `GOROOT`, so they can't be the same directory. This README will assume you are using a folder named `go/` for `GOROOT` and `gocode/` for for `GOPATH`.

So first, decide where you want these folders to be:
1. By default, the MacOS package installer places the Go distribution to `/usr/local/go`
2. By default, the Windows installation places it at `c:\Go`.
3. You can also install the Go distribution somewhere else:
    1. So, if you don't want to use the default directories, you can make make these `GOPATH` and `GOROOT` directories where you want to (this can be in your home directory, in `/tigress/username/` for della, or `/jukebox/norman/username/` for spock.)
    2. If you do this, make sure you correctly set the path by adding the path to your go distribution to the `GOPATH` variable in `~/.bashrc` file


We have to update the ~/.bashrc script in your home directory to match wherever you want these directories to be.

```
nano ~/.bashrc
```

Add the following lines:
```
export GOROOT="PATH/TO/CODE/go"
export GOPATH="PATH/TO/CODE/gocode"
export PATH=$GOROOT/bin:$PATH
```

Make sure to run:

```
source ~/.bashrc
```
Or else start a new session so the paths are updated.

## Download Go
The first step to setting up emergent is to download Go. Pick your favored binary release [here](https://golang.org/dl/), download it, and run it. The MSI installers for Windows and Mac do all the work for you.

You should download versions 1.13 or later.

### Downloading Go on the cluster


To download go on the cluster, run
```
wget https://dl.google.com/go/go1.14.1.linux-amd64.tar.gz
```
In general, to download a file, just run `wget` and then the download link. The above command downloads a .tar.gz file wherever you are. You probably want to run it from /PATH/TO/CODE, or else move the tar.gz file there after you download it.



## Install Go
When you ran `wget https://dl.google.com/go/go1.14.1.linux-amd64.tar.gz`it created a .tar.gz file wherever you ran it. Move it to where you want the GOPATH directory to be, if you didn't run the command there already. Then unzip the file:

```
tar -xzf go1.14.1.linux-amd64.tar.gz
```
That should have created a folder called `go/`. Change the name to be whatever you called the `GOPATH` directory (i.e. gocode/).

Then make the directory for the `GOROOT`:

```
mkdir PATH/TO/CODE/go
```

Now you should have two folders that match `GOPATH` and `GOROOT`

## Test Your Go Installation
You might want to make sure you installed Go successfully. To do this:


1. Create a folder `PATH/TO/CODE/gocode/src/hello`
    1. Then create a file named `hello.go` with the following content:

```
package main

import "fmt"

func main() {
	fmt.Printf("hello, world\n")
}
```
2. 'Build'  the script by executing the command:

```
go build
```

within your `hello` folder. This creates a `hello.exe` file in your directory that you can then run to execute the code.
3. Run `hello.exe`. (You can do this in your Mac terminal with `./hello` and in windows with `hello`. If the command returns `hello, world`, you're golden. If not, try running `echo $PATH` to see if `GOROOT` and `GOPATH` were added correctly.


Next, add the following lines to your `~/.bashrc` This disables gomod, which is a new feature in Go 1.14 but causes problems for us

```
export GO111MODULE=off

gomod() {
    export GO111MODULE=on
}

nogomod() {
    export GO111MODULE=off
}

```


## Install the GoGi toolkit

The GoGi GUI framework is responsible for the graphical user interface that visualizes our models and the interface for interacting with them. It can be set up using your go install. Depending on your initial success and operating system, you may have to install some additional materials before installing the toolkit:

### Windows Pre-Installation Steps
The Windows install requires a "mingw compatible `gcc`" - a compiler for C, which Go is based on. The install wiki recommends [this one](http://tdm-gcc.tdragon.net/download).

### MacOS Pre-Installation Steps
The MacOS install requires you first have XCode and relevant header files installed (e.g. gcc to compile go programs). You only need to do this once. This can be achieved two commands:
```
> xcode-select --install
> open /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg
```

### Cluster Pre-Installation steps
If you're on the cluster, make sure that the correct version of gcc is loaded. The gcc default version on spock is too outdated, so run
```
module load rh/devtoolset/8
```

### Toolkit Installation
No matter your OS, you have to complete these steps to execute the actual installation.
1. Start with the terminal command
    1. Note: You may or may not see a warning, for example: "no Go files in..."; you can safely ignore the warning
```
go get github.com/goki/gi
```
2. Next, we ensure that all dependencies are installed/updated in the relevant `examples/widgets` directory:
```
> cd PATH/TO/CODE/gocode/src/github.com/goki/gi/examples/widgets
> go get -u ./...
```
The location of the relevant directory could depend on your OS and Go path settings.

The [`goki` install page](https://github.com/goki/gi/wiki/Install) includes some troubleshooting tips if you had trouble here. Or you can reach out to me!

## Install leabra
`leabra` is considered the basic template/starting point for creating your own simulations, with the Go and Python versions closely matched in functionality to support development along either direction. The install process for it is pretty similar to that for the GoGi toolkit!

Either run 1 or 2. Do not run both!!

1. If you'd like to install the official version of leabra
    1. Execute in your terminal.
    Again, ignore any warnings about no go files, etc.
```
go get github.com/emer/leabra
```

2. If you'd like to install a different version of leabra (e.g. private-leabra), `cd` into the correct directory in `GOPATH`
    1. Make sure the correct branch is checked out  
```
cd PATH/TO/CODE/gocode/src/github.com/
mkdir emer
cd emer
git clone https://github.com/PrincetonCompMemLab/private-leabra.git leabra
git clone https://github.com/PrincetonCompMemLab/private-emergent.git emergent
cd leabra
git checkout origin/dev
git checkout dev
```


3. Ensure all dependencies are installed in the relevant `examples/ra25` directory with these steps, again modifying paths to suit your settings and OS (you may want to do this for your particular model, instead of ra25):
```
> cd PATH/TO/CODE/gocode/src/github.com/emer/leabra/examples/ra25
> go get -u ./...
```
4. Finally, to actually run the simulation, you build and run the executable associated with the script:
```
> cd PATH/TO/CODE/gocode/src/github.com/emer/leabra/examples/ra25
> go build
> ./ra25
```

Your setup has been successful if that last statement generates a window like the one at the top of this guide. In general, the expected process for making simulations via emergent is to copy the `ra25.go` code to your own repository and modify it according to your specifications. When you're done you run `go build` to turn the modified code into an executable simulation just like in step 3 above!

## Adding Python Support
One of the most exciting possibilities realized by the new emergent, though, is the option to avoid developing your simulation in Go and instead write your code with Python. The anticipated development process is quite similar (you'll just be editing and executing a Python-based implementation of leabra), but extra installation steps are necessary to support integrating Python into the framework. Unfortunately, neither I nor the emergent developers have figured out how to make this extra functionality work with Windows yet, so at least for now these instructions are MacOS/Unix specific. Indeed, even the instructions presented here are temporary and likely to change after further updates to emergent's components.

To complete the process, you'll need `pkg-config`. One of the easiest ways to install it is with the homebrew command `brew install pkg-config`. If you don't have homebrew, you can get it with the command `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`.

1. First, make sure you have the [latest version of Python](https://www.python.org/downloads/).
2. Next, execute these commands in your terminal installing some key dependencies.
```
> python3 -m pip install --upgrade pybindgen setuptools wheel pandas
> go get golang.org/x/tools/cmd/goimports
> go get github.com/go-python/gopy
> go get github.com/goki/gopy
```
3. These packages may need to be built into executables and added to your `~/go/bin` directory:
```
> cd ~/go/src/golang.org/x/tools/cmd/goimports
> go install
> cd ~/go/src/github.com/go-python/gopy
> git fetch origin pull/180/head:pr180
> git checkout pr180
> go install
```
Check if `gopy.exe` and `goimports.exe` have been successfully added to your `~/go/bin` directory. If not, you may have to add them yourself. The command `go build` in the respective directory will generate the relevant `.exe` file and you will be able to copy it over yourself. `open .` will open the terminal's current directory in your file browser.

Similarly, if along this process you obtain an error message about a missing `gopyh` module, may also need to manually relocate the `gopyh` folder at `~/go/src/github.com/goki/gopy` to the `~/go/src/github.com/go-python/gopy` directory.

4. Next we install some more Python-sided interface dependencies.
```
> cd ~/go/src/github.com/goki/gi/python
> sudo make
> sudo make install
```

5. The penultimate step sets up and places `pyleabra.exe` and `pyemergent.exe` into your `usr/local/bin` directory.
```
> cd ~/go/src/github.com/emer/leabra/python
> sudo make
> sudo make install
```

6. Finally, we execute the python version of ra25. If this opens an interface and begins a simulation, then your installation was successful.
```
> cd ../examples/ra25
> pyleabra -i ra25.py
```
