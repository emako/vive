# vive

This repository builds a single-file `vive.exe` from [thebookisclosed/ViVe](https://github.com/thebookisclosed/ViVe).

Upstream source stays in the `src` submodule. `vive.patch` is applied before compilation. The published program is one executable, `vive.exe`.

Upstream is licensed under GPL-3.0-or-later. See `src/LICENSE`.

## Modifications

`vive.patch` makes these changes:

- Converts `ViVe/ViVe.csproj` and `ViVeTool/ViVeTool.csproj` to SDK-style projects (`Microsoft.NET.Sdk`, `net481`).
- Merges referenced assemblies into the executable with Costura.Fody (`Albacore.ViVe.dll`, `Newtonsoft.Json.dll`, and their dependencies).
- Embeds `Extra/FeatureDictionary.pfs` as a manifest resource named `FeatureDictionary.pfs` and reads the dictionary from that resource.
- Sets the executable name to `vive.exe`. The Release output directory contains that file and nothing else.
- Leaves `/dictupdate` able to compare the embedded dictionary with upstream, and tells you to install a newer build instead of writing a sidecar `.pfs`.

## Releases

GitHub Actions builds on every push. Tag text is not validated.

| Push | GitHub Release | Version |
| --- | --- | --- |
| Any commit that is not a tag | Prerelease, replaced on the next commit | `latest` |
| Any tag | Stable release | the tag name |

The only asset is `vive.exe`. The moving preview uses the tag name `latest`. A tag you push yourself, including a tag named `latest`, is published as a stable release.

## Local build

The patch matches the submodule commit recorded in this repository.

```powershell
git submodule update --init
git -C src apply --whitespace=nowarn ../vive.patch
dotnet build src/ViVeTool/ViVeTool.csproj -c Release
```

The executable is `src/ViVeTool/bin/Release/vive.exe`. It needs the .NET Framework 4.8.1 runtime, the same requirement as upstream. No targeting pack is required to compile: the projects restore `Microsoft.NETFramework.ReferenceAssemblies.net481`.
