# Swift Package Manager Runner

This script manages cloning, building, and running Swift packages via the Swift Package Manager.

## Installation

Copy the `spm` shell script in this repo to your local machine. The directory you save the `spm` shell script to will be referred to as `SPM_ROOT` in the discussion below.

## Initialization

Run `spm init repo_url`. This clones the Git repository at `repo_url` to `SPM_ROOT/.spm/repo_name` where `repo_name` is the name of the repo at `repo_url`.

For example, to clone [SwiftLint](https://github.com/realm/SwiftLint), run:

```
spm init https://github.com/realm/SwiftLint
```

This will clone the repo to `SPM_ROOT/.spm/SwiftLint`.

If `SPM_ROOT` is inside a Git repository itself, you should consider adding `.spm` to your `.gitignore`.

## Running

Run `spm run repo_name tag executable [arguments]` to build the specified tag and run the generated executable. This will perform a `git fetch` if the specified tag is not available in the local clone of the repository. `swift build` is used to perform an incremental build of the package. The resulting executable is invoked directly (i.e., not via `swift run`).

For example, to run [SwiftLint](https://github.com/realm/SwiftLint) at tag 0.47.1, run:

```
spm run SwiftLint 0.49.1 swiftlint --version
```

This will check out tag 0.49.1 in `SPM_ROOT/.spm/SwiftLint`. If that tag does not exist, it will first perform a `git fetch`. It then calls `swift package build -c release` in `SPM_ROOT/.spm/SwiftLint`. Finally, it invokes `SPM_ROOT/.spm/SwiftLint/.build/release/swiftlint` from the current directory, passing `--version` as an argument to the executable.

## Script usage

Continuing the example of using `spm` to run SwiftLint, at the root of your Xcode project, make a `bin` directory. Next, save `spm` as `bin/spm` and make sure it is executable (via `chmod +x bin/spm`).

Create `bin/run-swiftlint.sh` with the following contents:

```bash
#!/usr/bin/env bash

version=0.49.1

script_dir="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
project_root="$( cd "$script_dir" && cd .. && pwd )"

cd "$project_root"

"$script_dir/spm" exists SwiftLint
if [ $? -ne 0 ]; then
    "$script_dir/spm" init https://github.com/realm/SwiftLint
fi

"$script_dir/spm" run SwiftLint $version swiftlint
```

Now you can add a Run Script Build Phase to your project that calls `bin/run-swiftlint.sh`. You will still need a `.swiftlint.yml` file to configure the directories that are scanned. Otherwise, SwiftLint will lint any packages managed by `spm`, including the SwiftLint source itself.

**Be sure to add `bin/.spm` to your `.gitignore`**

