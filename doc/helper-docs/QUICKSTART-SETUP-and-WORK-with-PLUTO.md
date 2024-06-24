# How to start playing with Pluto fast 😇

## Prerequisites


- **ALL THE DETAILS ARE EXPLAINED in great length HERE [doc/helper-docs/SETUP-PLUTO-DETAILS.md](SETUP-PLUTO-DETAILS.md)**
- For dependency Ubuntu packages, follow this **1.1. Installing Dev dependencies** section.
- For setting up `clang/llvm`, follow this **1.2. Handling `LLVM/Clang` dependency** section.
- This document is intended for highlighting, how can you [USE THE IDEAS FROM HERE](SETUP-PLUTO-DETAILS.md). And jump faster to start playing with **Pluto**.
- **DONOT WORK w/ THE `master` BRANCH.**


# Setting Up Pluto

## 1. Preparing the working repo (ONE TIME SETUP, cloning stuffs)

### 1.1. Clone (Donot use `--recursive` w/ `git clone`)

```sh
git clone https://github.com/pal-stdr/pluto-forked.git pluto-forked
cd pluto-forked/
```

### 1.2. Checkout your desired branch (`how-to-setup-pluto`, or `setup-nlohmann-json`, etc.)

- **Donot work w/ the `master` branch.**
- First check the branch list

```sh
git branch -la

# Returns
remotes/origin/HEAD -> origin/master
  remotes/origin/how-to-setup-pluto
  remotes/origin/master
  remotes/origin/setup-cJSON
```

- **Switch/`checkout` to your desired branch**

```sh
git checkout how-to-setup-pluto
# or
git checkout setup-nlohmann-json
# or any branch you like except master

```


### 1.3. Now load all the `submodules` (i.e. `openscop`, `cloog-isl`, etc...)

```sh
# For loading other libs (cloog-isl, openscop, etc.) nested inside pluto (In one command)
git submodule update --init

# If you want to do it in 2 separate command
git submodule init
git submodule update
```


## 2. Run `autogen.sh`, prepare `build-pluto-with-llvm.sh` with proper `clang` path, and then `configure` + `build` (ONE TIME SETUP, condition applied)

- For step `2.1`, you donot have to do it again unless you change your `LLVM_FOR_PET_INSTALLATION_ROOT=/path/to/clang/bin` path.
- For step `2.2`, you donot have to do it again unless you change `Makefile.am` or `configure.ac` from any `submodule`.


### 2.1. Update the `build-pluto-with-llvm.sh` with proper `LLVM_FOR_PET_INSTALLATION_ROOT=/path/to/your/llvm-16-src-build/installation`

- **ONE TIME SETUP**, unless unless you change `LLVM_FOR_PET_INSTALLATION_ROOT=/path/to/your/llvm-16-src-build/installation` path.
- Check the **2.5. Prepare the following `shell` script for installing Pluto`** section from [doc/helper-docs/SETUP-PLUTO-DETAILS.md](SETUP-PLUTO-DETAILS.md).
- set `LLVM_FOR_PET_INSTALLATION_ROOT=/path/to/your/llvm-16-src-build/installation` in `build-pluto-with-llvm.sh`


### 2.2. Run `autogen.sh` to generate `configure` files for all `submodules` and `pluto` lib itself

- **ONE TIME SETUP**, unless unless you change any `Makefile.am` or `configure.ac`

- Run `./autogen.sh`. It will generate all the `configure` scripts for each submodule + pluto by reading all `Makefile.am` or `configure.ac` from each submodules.

```sh
./autogen.sh
```

### 2.3. Update the `build-pluto-with-llvm.sh` with proper `WANT_TO_CONFIGURE_AND_BUILD=1` settings

**IMPORTANT:**
- **FOR THE `VERY FIRST TIME`, YOU HAVE TO RUN IT WITH `WANT_TO_CONFIGURE_AND_BUILD=1`. Means, it will gen `Makefile` by reading `configure` files from each submodules. And then run the actual build process (i.e. compilation).**

```sh
# One time
chmod +x build-pluto-with-llvm.sh

# Then run (w/ WANT_TO_CONFIGURE_AND_BUILD=1)
./build-pluto-with-llvm.sh
```

## 3. Play with Pluto 🤠

- **I assume, you already configured + build Pluto once w/ `WANT_TO_CONFIGURE_AND_BUILD=1`.**
- **So now set it to `WANT_TO_CONFIGURE_AND_BUILD=0`. This will make sure that you only compile Pluto after changing the actual code each time. 😇**

```sh
# WANT_TO_CONFIGURE_AND_BUILD=0
# Just change Pluto code and Run the script again and again only to compile
./build-pluto-with-llvm.sh
```


## 4. How to use `Pluto`

Though the `--prefix=` is set to `pluto/installation`, so the bins can be found in `./installation/bin/` dir

### 4.1. Using `pluto` bin

- Usage format `installation/bin/pluto <input.c> [options] -o <output.c>`. Example `./installation/bin/pluto test/matmul.c -o test/transformed_matmul.c`


### 4.2. Using generated `polycc` script bin (`installation/bin/polycc`)

- Usage format `installation/bin/polycc <input.c> [options] -o <output.c>`. Example `./installation/bin/polycc test/matmul.c -o test/transformed_matmul.c`

- Remember `polycc` is shell script which act as a wrapper around `pluto`. It handles the optimization flags/options for this pluto release.

- Actually this `polycc` points to `build/tool/pluto`. **So if you want to use `polycc`, you cannot delete the `build/` dir**. Neither you will get error like `/pluto/build/tool/pluto: No such file or directory`. See the following snippet collected from inside of the `polycc`

```sh
pluto=/path/to/pluto/build/tool/pluto
inscop=/path/to/pluto-test-v2/build/../inscop

# check for command-line options
for arg in $*; do
    if [ $arg == "--parallel" ]; then
        PARALLEL=1
    elif [ $arg == "--parallelize" ]; then
        PARALLEL=1
    elif [ $arg == "--unroll" ]; then
        UNROLL=1
    elif [ $arg == "--debug" ]; then
        DEBUG=1
    elif [ $arg == "--moredebug" ]; then
        DEBUG=1
    elif [ $arg == "-i" ]; then
        INDENT=1
    elif [ $arg == "--indent" ]; then
        INDENT=1
    elif [ $arg == "--silent" ]; then
        SILENT=1
    fi
done

```

### 4.3. Some of the `flags` for `pluto`

- Default for this `0.12.0-2-g001cb37` version (for details check [ChangeLog](ChangeLog))

```sh
- Introduced loop unroll and jam support using ClooG AST and enable it by
  default.
- Improvements to intra-tile optimization heuristics.
- Support for the LP based decoupled framework, Pluto-lp-dfp, included;
  requires Pluto to be configured with GLPK or Gurobi.
- Two more fusion models, namely, typed and hybrid fusion incorporated. These
  models use the decoupled framework work.
- libpluto interface updated.
- --tile and --parallel enabled by default
- Diamond tiling enabled by default
- Auto-indentifucation of time-iterated stencils with concurrent start.
- Code modernized for compilation with C++ compilers; language changed to C99
  and C++11 for all future development
- src/ split into lib/ and tool/ to separate out libpluto and the pluto tool.
```

- Available `Optimization` flags

```sh

Optimizations          Options related to optimization
       --tile                    Tile for locality [disabled by default]
       --[no]intratileopt        Optimize intra-tile execution order for locality [enabled by default]
       --second-level-tile       Tile a second time (typically for L2 cache) [disabled by default] 
       --determine-tile-size    Choose tile sizes using a tile size selection model
       --cache-size=<value>    Cache size per core in bytes for first level of tiling. Default 1MB (L2 cache size))
       --data-element-size=<value>  Size of each data element in bytes. Default sizeof(double)
       --parallel                Automatically parallelize (generate OpenMP pragmas) [disabled by default]
    or --parallelize
       --[no]diamond-tile        Performs diamond tiling (enabled by default)
       --full-diamond-tile       Enables full-dimensional concurrent start
       --per-cc-obj              Enables separate dependence distance upper bounds for dependences from different connected components
       --[no]prevector           Mark loops for (icc/gcc) vectorization (enabled by default)
       --multipar                Extract all degrees of parallelism [disabled by default];
                                    by default one degree is extracted within any schedule sub-tree (if it exists)
       --innerpar                Choose pure inner parallelism over pipelined/wavefront parallelism [disabled by default]

   Fusion                Options to control fusion heuristic
       --nofuse                  Do not fuse across SCCs of data dependence graph
       --maxfuse                 Maximal fusion
       --smartfuse [default]     Heuristic (in between nofuse and maxfuse)
       --typedfuse               Typed fusion. Fuses SCCs only when there is no loss of parallelism [uses dfp framework and requires glpk or gurobi for solving LPs]
       --hybridfuse              Typed fusion at outer levels and max fuse at inner level [uses dfp framework and requires glpk or gurobi for solving LPs]
       --delayedcut              Delays the cut between SCCs of different dimensionalities in dfp approach [uses glpk or gurobi for solving LPs]
```