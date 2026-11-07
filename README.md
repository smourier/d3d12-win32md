# d3d12-win32md
Generator project for `Microsoft.Direct3D.D3D12.winmd`, built from the headers of the [DirectX 12 Agility SDK](https://devblogs.microsoft.com/directx/directx12agility/),
using the out-of-tree SDK for [win32metadata](https://github.com/microsoft/win32metadata).

## Why
Since version 71.0.45, `Microsoft.Windows.SDK.Win32Metadata` ships the `Windows.Win32.Graphics.Direct3D12` namespace in its own `Microsoft.Direct3D.D3D12.winmd`,
generated from the Windows SDK headers only ([microsoft/win32metadata#2399](https://github.com/microsoft/win32metadata/pull/2399)).
Interfaces and structures that only exist in the Agility SDK, such as `ID3D12Device15`, `ID3D12RootSignature1` or `D3D12_FEATURE_DATA_D3D12_OPTIONS22`,
are not in it anymore.

This project generates a replacement for that file from the `Microsoft.Direct3D.D3D12` NuGet package.
It keeps the file name, the namespaces and the `1.0.0.0` assembly version of the original, so `Windows.Win32.winmd` references resolve into it.
A projection uses it *instead* of the one in the Win32Metadata package, never next to it, since both define the same types.

## Getting Started
Install the [dotnet CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/) and the Visual Studio C++ build tools if you don't already have them.

> [!NOTE]
> although it's a Visual Studio project, Visual Studio's "Build" command won't work

So, clone this repo and run the following command from the repo root:

```cmd
> dotnet build
```
The build may take a few minutes, but when it's done the winmd file should be in `bin\Microsoft.Direct3D.D3D12.winmd`.

> [!TIP]
> In case of build issues, make sure the **ClangSharpPInvokeGenerator** dotnet tool is not installed globally.

## What is generated
Everything goes in `Windows.Win32.Graphics.Direct3D12`, from `d3d12.h`, `d3d12sdklayers.h`, `d3d12shader.h`, `d3d12compiler.h` and `d3dshadercacheregistration.h`.
The shader cache registration types used to be in `Windows.Win32.Graphics.Direct3D`, but `Windows.Win32.winmd` owns that namespace now.

Everything else these headers use, DXGI, the common Direct3D types or the Windows foundation types, stays a reference to `Windows.Win32.winmd`.
The `d3d11shader.h` constants that `d3d12shader.h` repeats are left out, see [exclusions.rsp](./exclusions.rsp).

The dll names, documentation links and annotations come from the settings Microsoft builds its own winmds with.
The build downloads them from the [win32metadata](https://github.com/microsoft/win32metadata) release named by `Win32MetadataSettingsTag`,
which should follow the version of the `Microsoft.Windows.WinmdGenerator` SDK.

## Updating the Metadata
When a new Agility SDK is released, update this version number in [D3D12Win32Metadata.csproj](./D3D12Win32Metadata.csproj):
```xml
    <AgilitySdkVersion>1.620.1</AgilitySdkVersion>
```

## Dependencies
* [dotnet CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/)
* [win32metadata](https://github.com/microsoft/win32metadata)
* [DirectX 12 Agility SDK](https://www.nuget.org/packages/Microsoft.Direct3D.D3D12)

## Related Projects
* [DirectNAot](https://github.com/smourier/DirectNAot)
* [webview2-win32md](https://github.com/smourier/webview2-win32md)
