# Mamba - MOOSE related operations cheat sheet

One of the first things that can be frustrating is that much of mamba's functionality is not
listed when passing `-h` command line argument. Below are a few commands to help you find your way.

## 1 The basic help
```bash
mamba -h
```
Example output:
```
usage: mamba [-h] [-V] command ...

conda is a tool for managing and deploying applications, environments and packages.

Options:

positional arguments:
  command
    clean        Remove unused packages and caches.
    compare      Compare packages between conda environments.
    config       Modify configuration values in .condarc. This is modeled after the git config command. Writes to the user .condarc file (/Users/username/.condarc) by default. Use the --show-sources flag to display all identified configuration locations on your
                 computer.
    create       Create a new conda environment from a list of specified packages.
    info         Display information about current conda install.
    init         Initialize conda for shell interaction.
    install      Installs a list of packages into a specified conda environment.
    list         List installed packages in a conda environment.
    package      Low-level conda package utility. (EXPERIMENTAL)
    remove       Remove a list of packages from a specified conda environment.
    rename       Renames an existing environment.
    run          Run an executable in a conda environment.
    search       Search for packages and display associated information.The input is a MatchSpec, a query language for conda packages. See examples below.
    uninstall    Alias for conda remove.
    update       Updates conda packages to the latest compatible version.
    upgrade      Alias for conda update.
    notices      Retrieves latest channel notifications.
    repoquery    Query repositories using mamba.

options:
  -h, --help     Show this help message and exit.
  -V, --version  Show the conda version number and exit.

conda commands available from other packages:
  env
```

## 2 Seeing what environments you have available
```bash
mamba env list
```

Example output:
```
base                  *  /Users/username/mambaforge3
moose                    /Users/username/mambaforge3/envs/moose
moose-devel              /Users/username/mambaforge3/envs/moose-devel
moose-devel2             /Users/username/mambaforge3/envs/moose-devel2
```

## 3 Creating a new environment
In general the command to create a new environment is as follows:
```bash
mamba create -n <environment_name> <dependencies>
```
Generally you'd probably want to do the MOOSE thing
```bash
mamba create -n enviro moose-dev
```
assuming you've added the channel for it.

## 4 Switching between environments
Switching mamba environments is done using the `activate` and `deactivate` commands, e.g.,
activating environment `enviro`,
```bash
mamba activate enviro
```
and to deactivate the current environment,
```bash
mamba deactivate
```

Note here that we didn't specify which environment to deactivate. This is because there can always
only be one mamba environment active.

## 5 Deleting an environment
Unlikely to be needed a lot, but here it is
```bash
mamba env remove -n <environment_name>
```

## 6 Adding a conda channel
Adding a channel is sometimes needed to get access to certain packages. This is definetely needed
for the MOOSE packages.
```bash
conda config --add channels https://conda.software.inl.gov/public
```


## 7 Command to show active conda channels
`mamba` is based off `conda` and some of conda's functionality is only available via conda commands. To see the channels you've added to your conda environment the following command can be executed
```bash
conda config --show channels
```
Example output:
```
channels:
  - https://conda.software.inl.gov/public
  - conda-forge
```

## 8 Search for a mamba env
Within the channels currently loaded, the following command will detail all the versions of a
package:
```bash
mamba search moose-dev
```
Example output:
```
Loading channels: done
# Name                       Version           Build  Channel
moose-dev                 2023.05.02         build_0  public
moose-dev                 2023.06.07         build_0  public
moose-dev                 2023.06.22         build_0  public
moose-dev                 2023.06.23         build_0  public
moose-dev                 2023.07.26         build_0  public
moose-dev                 2023.08.29         build_0  public
moose-dev                 2023.09.06         build_0  public
moose-dev                 2023.09.13         build_0  public
moose-dev                 2023.09.15         build_0  public
moose-dev                 2023.09.19         build_0  public
moose-dev                 2023.10.06         build_0  public
moose-dev                 2023.10.23         build_0  public
moose-dev                 2023.10.24         build_0  public
```

## 9 See all the dependencies of MOOSE-dev
In general you can just execute this
```bash
mamba repoquery depends -a <package_name>=<package_version>
```

For MOOSE you'd probably want to do this:
```bash
mamba repoquery depends -a moose-dev=2023.09.15
```

This will probably list just the first few levels of dependencies.

## 10 See the version of an environment you have
Unfortanely you will have to load it first
```bash
mamba activate moose-devel2
```
Then you can run
```bash
mamba list | grep moose
```
Example output:
```
moose-dev                 2023.07.26              build_0    https://con..etc
moose-libmesh             2023.07.26              build_0    https://con..etc
moose-libmesh-vtk         9.2.6                hbb36488_1    https://con..etc
moose-mpich               4.0.2                   build_8    https://con..etc
moose-petsc               3.16.6                  build_8    https://con..etc
moose-test-tools          2023.05.12      py310h51c69a1_0    https://con..etc
moose-tools               2023.05.12      py310h51c69a1_0    https://con..etc
moose-wasp                2023.05.01              build_0    https://con..etc
```

## 10 Cloning an environment (used for downgrading as well)
Because of all the dependencies there is no easy way to downgrade a specific environment. The best solution is to maintain a list of old environments by using the cloning functionality of mamba.
To clone an environment, use the following:
```bash
mamba create -n <environment_name> --clone <old_environment_name>
```

Example: Suppose I want to update my current MOOSE environment but I have some other projects that
still need the older version. Before I update, I could do this:
```bash
mamba create -n moose-dev-old-2023-09 --clone moose-dev
```
