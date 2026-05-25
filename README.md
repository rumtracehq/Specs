# Rumtrace CocoaPods Spec Repo

This repo hosts the **public podspec** for Rumtrace iOS components whose
**source code lives in private GitHub repositories**.

Publishing a podspec here lets developers install Rumtrace pods from any
project — including npm consumers of `@rumtrace/rumtrace-rn-ios` — without
needing CocoaPods Trunk to access the private source. Each consumer's git
credentials (SSH key or HTTPS PAT with read access to the private repo) are
used at `pod install` time to clone the source.

## Usage in a Podfile

```ruby
source 'https://github.com/rumtracehq/Specs.git'
source 'https://cdn.cocoapods.org/'

platform :ios, '16.0'

target 'YourApp' do
  use_frameworks!
  # …
  # `RumtraceIosSdk` (and anything that depends on it) will resolve here.
end
```

## Layout

```
Specs/
└── RumtraceIosSdk/
    ├── 0.9.4/
    │   └── RumtraceIosSdk.podspec
    └── …/
```

CocoaPods accepts both `.podspec` (Ruby) and `.podspec.json` files; we keep
the Ruby form so podspecs remain easy to read in PRs.

## Publishing a new version

From the SDK repo (after tagging `vX.Y.Z`):

```sh
# one-time, register this repo with CocoaPods
pod repo add rumtrace-specs git@github.com:rumtracehq/Specs.git

# every release
pod repo push rumtrace-specs RumtraceIosSdk.podspec --allow-warnings
```

`pod repo push` lints the spec, copies it into
`~/.cocoapods/repos/rumtrace-specs/RumtraceIosSdk/<version>/`, commits, and
pushes to GitHub.

If you don't use `pod repo push`, the manual equivalent is:

```sh
VERSION=$(grep -E 'spec\.version\s*=' RumtraceIosSdk.podspec | sed -E 's/.*"([^"]+)".*/\1/')
mkdir -p path/to/Specs/RumtraceIosSdk/$VERSION
cp RumtraceIosSdk.podspec path/to/Specs/RumtraceIosSdk/$VERSION/
cd path/to/Specs && git add . && git commit -m "RumtraceIosSdk $VERSION" && git push
```

## What stays private

- Source code in `rumtracehq/rumtrace-ios-sdk`
- Tags / releases on that repo

## What is public

- **This** repo (the podspec metadata only)
- The list of versions and their dependency declarations
