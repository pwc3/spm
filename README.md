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
spm run SwiftLint 0.47.1 swiftlint --version
```

This will check out tag 0.47.1 in `SPM_ROOT/.spm/SwiftLint`. If that tag does not exist, it will first perform a `git fetch`. It then calls `swift package build` in `SPM_ROOT/.spm/SwiftLint`. Finally, it invokes `SPM_ROOT/.spm/SwiftLint/.build/debug/swiftlint` from the current directory, passing `--version` as an argument to the executable.

