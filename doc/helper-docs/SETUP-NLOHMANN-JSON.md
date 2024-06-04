# Nlohmann json (for `C++`) setup as Submodule (`v3.11.3`)

**The doc is here for if you want to setup/integrate [json](https://github.com/nlohmann/json) lib as submodule with pluto. If it already added, donot need to add again.**


## 1. Prepare `json` as a github hosted repository with necessary files.

- **We have to set the [`json`](https://github.com/nlohmann/json) submodule for a specific tag `v3.11.3`. It's not a `branch`. So actual process is quite different.**
- You donot need to change anything in [`json`](https://github.com/nlohmann/json).
- Prepare a separate git branch for submodule purpose.
- For `json`, could be found at [`json`](https://github.com/nlohmann/json/tree/v3.11.3) in `v3.11.3` tag.



## 2. Now add it as a `git` submodule in the parent repo (i.e. `pluto-forked`)

### 2.1.  Run the following `git submodule add` command.
```sh
git submodule add --name json https://github.com/nlohmann/json.git json

# Returns
Cloning into '/path/to/parent-repo/json'...
remote: Enumerating objects: 38209, done.
remote: Counting objects: 100% (91/91), done.
remote: Compressing objects: 100% (46/46), done.
remote: Total 38209 (delta 43), reused 69 (delta 33), pack-reused 38118
Receiving objects: 100% (38209/38209), 185.18 MiB | 8.21 MiB/s, done.
Resolving deltas: 100% (23463/23463), done.
```

**Explanation:**
You will see the `json/` is cloned to your parent repo. But it is pointing to default branch (i.e. `develop` for the `json` lib case). There it will automatically create git `index` and the following snippet inside `.gitmodules` file. You don't have to do anything manually. Donot worry about all the dumped files in the `json/` submodule.

```sh
[submodule "json"]
	path = json
	url = https://github.com/nlohmann/json.git
```


### 2.2. Go to the submodule dir.

```sh
cd json/
```

### 2.3. Fetch all git `tags` for `json` submodule

```sh
git fetch --tags
```

### 2.4. Checkout your desired version/tag

```sh
git checkout tags/v3.11.3

# Returns
Note: switching to 'tags/v3.11.3'.

You are in 'detached HEAD' state....
....
HEAD is now at 9cca280a JSON for Modern C++ 3.11.3 (#4222)
```

### 2.5. Go back to the parent repo

```sh
cd ..
```

### 2.6. git `stage` (i.e. `add`) the `json/` submodule

This will stage `tag` specific (i.e. `v3.11.3`) files.

```sh
git stage json/

git status

# Returns

On branch how-to-setup-pluto
Your branch is up to date with 'origin/how-to-setup-pluto'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   .gitmodules
        new file:   json
```

### 2.7. Now commit the changes

```sh
git commit -m "Add 'json' submodule for checked out tag/version v3.11.3"

# Returns
2 files changed, 4 insertions(+)
create mode 160000 json
```


### 2.8. Verify if the right tag (i.e. `v3.11.3`) is included

```sh
# Go to ".git/" dir
cd .git/

# Run grep search
grep -Rn . -e 'v3.11.3'

# Returns
./modules/json/packed-refs:77:9cca280a4d0ccf0c08f47a99aa71d1b0e52f8d03 refs/tags/v3.11.3
./modules/json/FETCH_HEAD:57:9cca280a4d0ccf0c08f47a99aa71d1b0e52f8d03   not-for-merge   tag 'v3.11.3' of https://github.com/nlohmann/json
./modules/json/logs/HEAD:2:8c391e04fe4195d8be862c97f38cfe10e2a3472e 9cca280a4d0ccf0c08f47a99aa71d1b0e52f8d03 Firstname Lastname <firstname.lastname@nomail.com> 1717432028 +0200        checkout: moving from develop to tags/v3.11.3
./logs/HEAD:3:fe6ac5c4902f8a4e2cc1cf99418c5cd36125eb30 b937db4811c82d68824352a550ca483b0e492a16 firstname lastname <firstname.lastname@nomail.com> 1717432117 +0200     commit: Add 'json' submodule for checked out tag/version v3.11.3
./logs/refs/heads/how-to-setup-pluto:2:fe6ac5c4902f8a4e2cc1cf99418c5cd36125eb30 b937db4811c82d68824352a550ca483b0e492a16 firstname lastname <pallab.sutradhar@nomail.com> 1717432117 +0200commit: Add 'json' submodule for checked out tag/version v3.11.3
./COMMIT_EDITMSG:1:Add 'json' submodule for checked out tag/version v3.11.3
```



## 3. DONOT NEED TO Add `json` dirname at `line 19` in `Makefile.am` (NOTHING TO DO IN THIS STEP. JUST TO EXPLAIN...)

**Because this lib is defined in pure header (i.e. `.hpp`) files.**
```sh
SUBDIRS = piplib $(MAY_ISL) lib polylib openscop cloog-isl clan candl pet tool
```



## 4. Then add the header search `-I` flag path with `pluto_CXXFLAGS` & linker path with `pluto_LDADD` for `json/` for  at `tool/Makefile.am`

### 4.1. Add the header search `-I` flag path as `-I$(top_srcdir)/` with `pluto_CXXFLAGS`. Typically at `line 47`. This ensures the header calling style `#include <nlohmann/json.hpp>`

```sh
pluto_CXXFLAGS = $(OPT_FLAGS) $(DEBUG_FLAGS) \
   -DSCOPLIB_INT_T_IS_LONGLONG -DCLOOG_INT_GMP -DPLUTO_OPENCL \
   -I../include \
   -I$(top_srcdir)/include \
   -I$(top_srcdir)/piplib/include \
   -I../clan/include \
   -I$(top_srcdir)/clan/include \
   -I$(top_srcdir)/pet/include \
   $(ISL_INCLUDE) \
   -I../cloog-isl/include \
   -I$(top_srcdir)/cloog-isl/include \
   -I../openscop/include \
   -I$(top_srcdir)/openscop/include \
   -I../candl/include \
   -I$(top_srcdir)/candl/include \
   -I../polylib/include \
   -I$(top_srcdir)/polylib/include \
   -I$(top_srcdir)/lib  \
   -I$(top_srcdir)/json/single_include  # for json
```



## 5. Then run the `./build-pluto-with-llvm.sh`



# How to use `json/`

- You just have to use the header call `#include <nlohmann/json.hpp>` or `#include <nlohmann/json_fwd.hpp>` or both anywhere inside the `tool/main.cpp` dir. Tested with `tool/main.cpp`

- If you want call it like `#include <json.hpp>`, then change `tool/Makefile.am` file's `pluto_CXXFLAGS` as following

```sh
pluto_CXXFLAGS = $(OPT_FLAGS) $(DEBUG_FLAGS) \
   -DSCOPLIB_INT_T_IS_LONGLONG -DCLOOG_INT_GMP -DPLUTO_OPENCL \
   -I../include \
   -I$(top_srcdir)/include \
   -I$(top_srcdir)/piplib/include \
   -I../clan/include \
   -I$(top_srcdir)/clan/include \
   -I$(top_srcdir)/pet/include \
   $(ISL_INCLUDE) \
   -I../cloog-isl/include \
   -I$(top_srcdir)/cloog-isl/include \
   -I../openscop/include \
   -I$(top_srcdir)/openscop/include \
   -I../candl/include \
   -I$(top_srcdir)/candl/include \
   -I../polylib/include \
   -I$(top_srcdir)/polylib/include \
   -I$(top_srcdir)/lib  \
   -I$(top_srcdir)/json/single_include/nlohmann  # For json
```