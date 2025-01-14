## This repository has been archived

The `windows-app` project was an experimental crate letting you call APIs from the Windows App SDK based on the technology from the `windows` crate. The Windows App SDK is however largely a vehicle for delivering out-of-band releases of Xaml, which is itself designed squarely for C# developers. While every effort has been made to support Rust, the Windows App SDK in its current form is too heavily tied to .NET and Visual Studio to be practically usable with other languages and toolchains. We hope this will change in future but for now this project has been archived. 

Many thanks to [@riverar](https://github.com/riverar) who almost singlehandedly drove the research and development for the `windows-app` project. 

## Rust for the Windows App SDK
The `windows-app` crate lets you call any [Windows App SDK](https://github.com/microsoft/WindowsAppSDK) (formerly known as Project Reunion) API using code generated from the metadata describing the API. It is powered by the [windows](https://github.com/microsoft/windows-rs) crate.

Note: This is an experimental 🧪 crate and is not ready for production use.

## Release channel coverage
The Windows App SDK is delivered via [three release channels](https://docs.microsoft.com/windows/apps/windows-app-sdk/release-channels)—experimental, preview, and stable. The `windows-app` crate currently targets APIs available in the preview and stable channels.

## Getting started
It's very early days for the `windows-app` crate. To try it out, add the following to your Cargo.toml file:

```toml
[build-dependencies.windows-app]
git = "https://github.com/microsoft/windows-app-rs"
features = [
    "WindowsAppSdk_Foundation"
]

[dependencies.windows]
version = "0.37"

[dependencies.windows-app]
git = "https://github.com/microsoft/windows-app-rs"
features = [
    "WindowsAppSdk_Foundation",
    "Windows_System_Power"
]
```

Add a build script (`build.rs`) to your crate to deploy the [Windows App SDK Bootstrapper](https://docs.microsoft.com/en-us/windows/apps/windows-app-sdk/deploy-unpackaged-apps?WT.mc_id=WD-MVP-5002756#using-features-at-run-time) with your app:

```rust
fn main() {
    ::windows_app::bootstrap::deploy::to_output_dir();
    
    // Temporary workaround for https://github.com/microsoft/WindowsAppSDK/issues/2634
    ::windows_app::build::embed_manifest(
        r#"<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
           <assembly manifestVersion="1.0" xmlns="urn:schemas-microsoft-com:asm.v1" />"#,
    )
}
```

Now make use of any Windows App SDK APIs as needed:

```rust
use ::windows_app::Microsoft::Windows::System::Power::*;
use ::windows_app::*;

fn main() -> ::windows::core::Result<()> {
    bootstrap::initialize()?;
    let charge = PowerManager::RemainingChargePercent()?;
    println!("Remaining charge: {charge}%");
    bootstrap::uninitialize()
}
```

Finally, install the [Windows App Runtime](https://docs.microsoft.com/windows/apps/windows-app-sdk/deploy-unpackaged-apps?WT.mc_id=WD-MVP-5002756) on all target machines:

1. Download the [Windows App Runtime Redistributable](https://aka.ms/windowsappsdk/1.1/1.1.1/windowsappruntimeredist-1.1.1.zip) package.
2. Execute `WindowsAppSDK-Installer-{arch}\WindowsAppRuntimeInstall.exe`.

After you install the Windows App Runtime, it will be kept up-to-date by Microsoft via Windows Update.
