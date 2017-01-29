# How to build static PRoot

```sh
make proot
```

The outcome will be:

- src/ - contains the source code for proot and all its dependencies
- pkg/ - contains the compiled library dependencies
- out/ - contains the statically linked proot executable

To change the proot code location to `https://github.com/myuser/PRoot.git`
(instead of the default `https://github.com/proot-me/PRoot.git`)
and use the given branch `myproot`:

```sh
make proot proot-url=https://github.com/myuser/PRoot.git proot-branch=myproot
```

To clean:

```sh
make clean
```
