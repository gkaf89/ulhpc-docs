# Self management of work environments in UL HPC with Conda

<!--intro-start-->

Packages provided through the standard channels of [modules](modules.md) and [containers](../containers/) are optimized for the ULHPC clusters to ensure their performance and stability. However, many packages where performance is not critical and are used by few users are not provided through the standard channels. These packages can still be installed locally by the users through an environment management system such as Conda.

!!! warning "Contact the ULHPC before installing any software with Conda"
	Prefer binaries provided through [modules](modules.md) or [containers](../containers/). Conda installs generic binaries that may be suboptimal for the configuration of the ULHPC clusters. Furthermore, installing packages locally with Conda consumes quotas in your or your project's account in terms of [storage space and number of files](/filesystems/quotas/#current-usage).
	
	Contact the ULHPC High Level Support Team in the [service portal](https://service.uni.lu/sp?id=index) [Home > Research > HPC > Software environment > Request expertise] to discuss possible options before installing any software.

[Conda](https://docs.conda.io/en/latest/) is an open source environment and package management system. With Conda you can create independent environments, where you can install applications such as python and R, together with any packages which will be used by these applications. The environments are independent, with the Conda package manager managing the binaries, resolving dependencies, and ensuring that package used in multiple environments are stored only once. In a typical setting, each user has their own installation of a Conda and a set of personal environments.

<!--intro-end-->

!!! important ""
    **TL;DR:** install and use the [Micromamba package manager](conda.md#the-micromamba-package-manager).

## A brief introduction to Conda

A few concepts are necessary to start working with Conda. In brief, these are package managers which are the programs used to create and manage environments, channels which are the repositories that contain the packages from which environments are composed, and distributions which are methods for shipping package managers.

### Package managers

Package managers are the programs that install and manage the Conda environments. There are multiple package managers, such as [`conda`](https://docs.conda.io/projects/conda/en/stable/), [`mamba`](https://mamba.readthedocs.io/en/latest/user_guide/mamba.html), and [`micromamba`](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html).

!!! important ""
    The UL HPC centre supports the use of [`micromamba`](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html) for the creation and management of personal Conda environments.

### Channels

Conda [channels](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/channels.html#what-is-a-conda-channel) are the locations where packages are stored. There are also multiple channels, with some important channels being:

- [`defaults`](https://repo.anaconda.com/pkgs/), the default channel,
- [`anaconda`](https://anaconda.org/anaconda), a mirror of the default channel,
- [`bioconda`](https://anaconda.org/bioconda), a distribution of bioinformatics software, and
- [`conda-forge`](https://anaconda.org/conda-forge), a community-led collection of recipes, build infrastructure, and distributions for the conda package manager.

The most useful channel that comes pre-installed in all distributions, is Conda-Forge. Channels are usually hosted in the [official Anaconda page](https://anaconda.org/), but in some rare occasions [custom channels](https://conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) may be used. For instance the [default channel](https://repo.anaconda.com/pkgs/) is hosted independently from the official Anaconda page. Many channels also maintain web pages with documentation both for their usage and for packages they distribute:

- [Default Conda channel](https://docs.anaconda.com/free/anaconda/reference/default-repositories/)
- [Bioconda](https://bioconda.github.io/)
- [Conda-Forge](https://conda-forge.org/)

### Distributions

Quite often, the package manager is not distributed on its own, but with a set of packages that are required for the package manager to work, or even with some additional packages that required for most applications. For instance, the `conda` package manager is distributed with the Miniconda and Anaconda distributions. Miniconda contains the bare minimum packages for the `conda` package manager to work, and Anaconda contains multiple commonly used packages and a graphical user interface. The relation between these distributions and the package manager is depicted in the following diagram.

[![](images/Miniconda-vs-Anaconda.jpg)](images/Miniconda-vs-Anaconda.jpg)

The situation is similar for [Mamba](https://mamba.readthedocs.io/en/latest/index.html) distributions. These distributions are supported by [Conda-Forge](https://github.com/conda-forge/miniforge), and their default installation options set-up `conda-forge` as the default and only channel during installation. The `defaults` or its mirror `anaconda` must be explicitly added if required. The distribution using the [Mamba](https://mamba.readthedocs.io/en/latest/user_guide/mamba.html) package manager was originally distributed as Mambaforge and was [recently renamed](https://github.com/conda-forge/miniforge#whats-the-difference-between-mambaforge-and-miniforge) to Miniforge. Miniforge comes with a minimal set of python packages required by the Mamba package manager. The distribution using the [Micromamba](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html) package manager ships no accompanying packages, as Micromamba is a standalone executable with no dependencies. Micromamba is using [`libmamba`](https://mamba.readthedocs.io/en/latest/index.html), a C++ library implementing the Conda API.

## The Micromamba package manager

[![](https://mamba.readthedocs.io/en/latest/_static/logo.png){: style="width:200px; margin-right:10px; float: left;"}](https://mamba.readthedocs.io/en/latest/index.html)

The [Micromaba](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html) package manager is a minimal yet fairly complete implementation of the Conda interface in C++, that is shipped as a standalone executable. The package manager operates strictly on the user-space and thus it requires no special permissions are required to install packages. It maintains all its files in a couple of places, so uninstalling the package manager itself is also easy. Finally, the package manager is also lightweight and fast.

!!! important ""
    **UL HPC provides support only for the Micromamba package manager.**

### Installation

A complete guide regarding Micromamba installation can be found in the [official documentation](https://mamba.readthedocs.io/en/latest/micromamba-installation.html). To install micromamaba in the HPC clusters, log in to Aion or Iris. Working on a login node, run the installation script,
```bash
"${SHELL}" <(curl -L micro.mamba.pm/install.sh)
``` 
which will install the executable and setup the environment. There are 4 options to select during the installation of Micromamba:

- The directory for the installation of the binary file:
  ```
  Micromamba binary folder? [~/.local/bin]
  ```
  Leave empty and press enter to select the default displayed within brackets. Your `.bashrc` script should include `~/.local/bin` in the `$PATH` by default.
- The option to add to the environment autocomplete options for `micromamba`:
  ```
  Init shell (bash)? [Y/n]
  ```
  Press enter to select the default option `Y`. This will append a clearly marked section in the `.bashrc` shell. Do not forget to remove this section when uninstalling Micromamba.
- The option to configure the channels by adding conda-forge:
  ```
  Configure conda-forge? [Y/n]
  ```
  Press enter to select the default option `Y`. This will setup the `~/.condarc` file with `conda-forge` as the default channel. Note that Mamba and Micromamba will not use the `defaults` channel if it is not present in `~/.condarc` like `conda`.
- The option to select the directory where environment information and packages will be stored:
  ```
  Prefix location? [~/micromamba]
  ```
  Press enter to select the default option displayed within brackets.

To setup the environment log-out and log-in again. Now you can use `micromamba`, including the auto-completion feature.

### Managing environments

As an example, the creation and use of an environment for R jobs is presented. The command,
```bash
micromamba create --name R-project
```
creates an environment named `R-project`. The environment is activated with the command
```bash
micromamba activate R-project
```
anywhere in the file system.

Next, install the base R environment package that contains the R program, and any R packages required by the project. To install packages, first ensure that the `R-project` environment is active, and then install any package with the command
```bash
micromamba install <package_name>
```
all the required packages. Quite often, the channel name must also be specified:
```bash
micromamba install --chanell <chanell_name> <package_name>
```
Packages can be found by searching the [conda-forge channel](https://anaconda.org/conda-forge).

For instance, the basic functionality of the R software environment is contained in the `r-base` package. Calling
```bash
micromamba install --channel conda-forge r-base
```
will install all the components required to run standalone R scripts. More involved scripts use functionality defined in various packages. The R packages are prepended with a prefix 'r-'. Thus, `plm` becomes `r-plm` and so on. After all the required packages have been installed, the environment is ready for use.

Packages in the conda-forge channel come with instructions for their installation. Quite often the channel is specified in the installation instructions, `-c conda-forge` or `--channel conda-forge`. While the Micromamba installer sets-up `conda-forge` as the default channel, latter modification in `~/.condarc` may change the channel priority. Thus it is a good practice to explicitly specify the source channel when installing a package.

After work in an environment is complete, deactivate the environment,
```bash
micromamba deactivate
```
to ensure that it does not interfere with any other operations. In contrast to [modules](modules.md), Conda is designed to operate with a single environment active at a time. Create one environment for each project, and Conda will ensure that any package that is shared between multiple environments is installed once.

Micromamba supports almost all the subcommands of Conda. For more details see the [official documentation](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html).

### Using environments in submission scripts

Since all computationally heavy operations must be performed in compute nodes, Conda environments are also used in jobs submitted to the [queuing system](/slurm/). Returning to the R example, a submission script running a single core R job can use the `R-project_name` environment as follows:
```
#SBATCH --job-name R-test-job
#SBATCH --nodes 1
#SBATCH --ntasks-per-node 1
#SBATCH --cpus-per-task 1
#SBATCH --time=0-02:00:00
#SBATCH --partition batch
#SBATCH --qos normal

echo "Launched at $(date)"
echo "Job ID: ${SLURM_JOBID}"
echo "Node list: ${SLURM_NODELIST}"
echo "Submit dir.: ${SLURM_SUBMIT_DIR}"
echo "Numb. of cores: ${SLURM_CPUS_PER_TASK}"

micromamba activate R-project

export OMP_NUM_THREADS=1
srun Rscript --no-save --no-restore script.R

micromamba deactivate
```

_Useful scripting resources_

- [Formatting submission scripts for R (and other systems)](/slurm/launchers.md#serial-task-script-launcher)

### Cleaning up package data

The Conda environment managers download and store a sizable amount of data to provided packages to the various environments. Even though the package data are shared between the various environments, they still consume space in your or your project's account. There are [limits in the storage space and number of files](/filesystems/quotas/#current-usage) that are available to projects and users in the cluster. Since Conda packages are self managed, **you need to clean unused data yourself**.

There are two main sources of unused data, the compressed archives of the packages that Conda stores in its cache when downloading a package, and the data of removed packages. All unused data in Micromoamba can be removed with the command
```bash
micromamba clean --all
```
that opens up an interactive dialogue with details about the operations performed. You can follow the default option, unless you have manually edited any files in you package data directory (default location `${HOME}/micromamba`).

??? info "Updating environments to remove old package versions"
	As we create new environments, we often install the latest version of each package. However, if the environments are not updated regularly, we may end up with different versions of the same package across multiple environments. If we have the same version of a package installed in all environments, we can save space by removing unused older versions.
	
	To update a package across all environments, use the command
	```bash
	for e in $(micromamba env list | awk 'FNR>2 {print $1}'); do micromamba update --name $e <package name>; done
	```
	and to update all packages across all environments
	```bash
	for e in $(micromamba env list | awk 'FNR>2 {print $1}'); do micromamba update --name $e --all; done
	```
	where `FNR>2` removes the headers in the output of `micromamba env list`, and is thus sensitive to changes in the user interface of Micromamba.
	
	After updating packages, the `clean` command can be called to removed the data of unused older package versions.

_Sources_

- [Oficial Conda `clean` documentation](https://docs.conda.io/projects/conda/en/latest/commands/clean.html)
- [Understanding Conda `clean`](https://stackoverflow.com/questions/51960539/where-does-conda-clean-remove-packages-from)

## Combining Conda with other package and environment management tools

It may be desirable to use Conda to manage environments but a different tool to manage packages, such as [`pip`](https://pip.pypa.io/en/stable/getting-started/). Or subenvironments may need to be used inside a Conda environment, as for instance with tools for creating and managing isolated Python installation, such as [`virtualenv`](https://virtualenv.pypa.io/en/latest/), or with tools for integrating managed Python installations and packages in project directories, such as [Pipenv](https://pipenv.pypa.io/en/latest) and [Poetry](https://python-poetry.org/).

Conda integrates well with any such tool. Some of the most frequent cases are described bellow.

### Managing packages with external tools

Quite often a package that is required in an environment is not available through a Conda channel, but it is available through some other distribution channel, such as the [Python Package Index (PyPI)](https://pypi.org/). In these cases the only solution is to create a Conda environment and install the required packages with `pip` from the Python Package Index.

Using an external packaging tool is possible because of the method that Conda uses to install packages. Conda installs package versions in a central directory (e.g. `~/micromamba/pkgs`). Any environment that requires a package links to the central directory with _hard links_. Links are added to the home directory (e.g. `~/micromamba/envs/R-project` for the `R-project` environment) of any environment that requires them. When using an external package tool, package components are installed in the same directory where Conda would install the corresponding link. Thus, external package management tools integrate seamlessly with Conda, with a couple of caveats:

- each package must be managed by one tool, otherwise package components will get overwritten, and
- packages installed by the package tool are specific to an environment and cannot be shared as with Conda, since components are installed directly and not with links.

!!! important "Prefer Conda over external package managers"
    Installing the same package in multiple environments with an external package tool consumes quotas in terms of [storage space and number of files](/filesystems/quotas/#current-usage), so prefer Conda when possible. This is particularly important for the `inode` limit, since some packages install a large number of files, and the hard links used by Conda do not consume inodes or [disk space](https://stackoverflow.com/questions/55566419/why-are-packages-installed-rather-than-just-linked-to-a-specific-environment).

#### Pip

In this example `pip` is used to manage packages in a Conda environment with [MkDocs](https://www.mkdocs.org/) related packages. To install the packages, create an environment
```bash
micromamba env create --name mkdocs
```
activate the environment,
```bash
micromamba activate mkdocs
```
and install `pip`
```bash
micromamba install --channel conda-forge pip
```
which will be used to install the remaining packages.

The `pip` will be the only package that will be managed with Conda. For instance, to update Pip activate the environment,
```bash
micromamba activate mkdocs
```
and run
```bash
micromaba update --all
```
to update all installed packaged (only `pip` in our case). All other packages are managed by `pip`.

For instance, assume that a `mkdocs` project requires the following packages:

- `mkdocs`
- `mkdocs-minify-plugin`

The package `mkdocs-minify-plugin` is less popular and thus is is not available though a Conda channel, but it is available in PyPI. To install it, activate the `mkdocs` environment
```bash
micromamba activate mkdocs
```
and install the required packages with `pip`
```bash
pip install --upgrade mkdocs mkdocs-minify-plugin
```
inside the environment. The packages will be installed inside a directory that `micromamba` created for the Conda environment, for instance
```
${HOME}/micromamba/envs/mkdocs
```
along side packages installed by `micromamba`. As a results, 'system-wide' installations with `pip` inside a Conda environment do not interfere with system packages.

!!! warning "Do not install packages in Conda environments with pip as a user"
    User installed packages (e.g.`pip install --user --upgrade mkdocs-minify-plugin`) are installed in the same directory for all environments, typically in `~/.local/`, and can interfere with other versions of the same package installed from other Conda environments.

#### Pkg

The Julia programming language provides its own package and environment manager, Pkg. The package manager of Julia provides many useful capabilities and it is recommended that it is used with Julia projects. Details about the use of Pkg can be found in the official [documentation](https://pkgdocs.julialang.org/v1/).

The Pkg package manager comes packages with Julia. Start by creating an environment,
```bash
mocromamba env create --name julia
```
activate the environment,
```bash
micromamba activate julia
```
and install Julia,
```bash
micromamba install --channel conda-forge julia
```
to start using Pkg.

In order to install a Julia package, activate the Julia environment, and start an interactive REPL session,
```bash
$ julia
julia>
```
by just calling `julia` without any input files.

- Enter the Pkg package manager by pressing `]`.
- Exit the package manager by clearing all the input from the line with backspace, and then pressing backspace one more time.

In the package manager you can see the status of the current environment,
```julia
(@julia) pkg> status
Status `~/micromamba/envs/julia/share/julia/environments/julia/Project.toml` (empty project)
```
add or remove packages,
```julia
(@julia) pkg> add Example
(@julia) pkg> remove Example
```
update the packages in the environment,
```julia
(@julia) pkg> update
```
and perform many other operations, such as exporting and importing environments from plain text files which describe the environment setup, and pinning packages to specific versions. The Pkg package manager maintains a global environment, but also supports the creation and use of local environments that are used within a project directory. The use of local environments is highly recommended, please read the [documentation](https://pkgdocs.julialang.org/v1/environments/) for more information.

After installing the Julia language in a Conda environment, the language distribution itself should be managed with `micromamba` and all packages in global or local environments with the Pkg package manager. To update Julia activate the Conda environment where Julia is stored and call
```bash
micromamba update julia
```
where as to update packages installed with Pgk use the `update` command of Pkg. The packages for local and global environments are stored in the Julia installation directory, typically
```
${HOME}/micromamba/envs/julia/share
```
if the default location for the Micromamba environment directory is used.

??? info "Advanced management of package data"
	Julia packages will consume [storage and number of files quota](/filesystems/quotas/#current-usage). Pkg uses automatic garbage collection to cleanup packages that are no longer is use. In general you don't need to manage then package data, simply remove the package and its data will be deleted automatically after some time. However, when you exceed your quota you need to delete files immediately.
	
	The _immediate removal_ of the data of uninstalled packages can be forced with the command:
	```julia
	using Pkg
	using Dates
	Pkg.gc(;collect_delay=Dates.Day(0))
	```
	Make sure that the packages have been removed from all the environments that use them
	
	_Sources_: [Immediate package data clean up](https://discourse.julialang.org/t/packages-clean-up-general-julia-data-consumption/56198)

_Useful resources_

- [Pkg documentation](https://pkgdocs.julialang.org/v1/)

### Combining Conda with external environment management tools

Quite often it is required to create isolated environments using external tools. For instance, tools such as [`virtualenv`](https://virtualenv.pypa.io/en/latest/) can install and manage a Python distribution in a given directory and export and import environment descriptions from text files. This functionalities allows for instance the shipping of a description of the Python environment as part of a project. Higher level tools such as [`pipenv`](https://pipenv.pypa.io/en/latest) automate the process by managing the Python environment as part of a project directory. The description of the environment is stored in version controlled files, and the Python packages are stored in a non-tracked directory within the project directory. Some wholistic project management tools, such as [`poetry`](https://python-poetry.org/), further integrate the management of the Python environment withing the project management workflow.

Installing and using in Conda environments tools that create isolated environments is relatively straight forward. Create an environment where only the required that tool is installed, and manage any subenvironments using the installed tool.

!!! important "Create a different environment for each tool"
    While this is not a requirement it is a good practice. For instance, `pipenv` and `poetry` used to and may still have conflicting dependencies; Conda detects the dependency and aborts the conflicting installation.

#### Pipenv

To demonstrate the usage of `pipenv`, create a Conda environment,
```bash
micromamba enc create --name pipenv
```
activate it
```bash
micromamba activate pipenv
```
and install the `pipenv` package
```bash
micromamba install --channel conda-forge pipenv
```
 as the only package in this environment. Now the `pipenv` is managed with Conda, for instance to update `pipenv` activate the environment
```bash
micromamba activate pipenv
```
and call
```bash
micromamba update --all
```
to update the single installed package. Inside the environment use `pipenv` as usual to create and manage project environments.
