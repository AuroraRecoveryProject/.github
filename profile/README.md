<p align="center">
  <img width="100%" src="./banner_wide.png">
</p>

![](banner.png)
![](logo.png)

<h1 align="center">Aurora Recovery Project (AURP)</h1>

<p align="center">
ARP is a new Android Recovery Project, powered by Flutter, based on [TWRP-16](https://github.com/TWRP-Test/android_bootable_recovery)
</p>

## Features

- Provides the full Flutter development experience, offering significantly higher development efficiency compared to traditional TWRP C++/XML development
- Includes access to the complete Flutter ecosystem, such as the fully featured terminal emulator [xterm.dart](https://github.com/TerminalStudio/xterm.dart), enabling more modern UI/UX designs
- Supports developing Recovery environments for multiple devices simultaneously
- Supports Flutter FFI plugins with arm64 implementations without requiring any modifications, such as [flutter_pty](https://github.com/TerminalStudio/flutter_pty)
- Supports both CPU and GPU rendering modes, both delivering very smooth frame rates

## Limitations

- Plugins containing Android/iOS native code are not supported
- Shader support is unavailable in pure CPU rendering mode
- More limitations to be added

## Repositories

- [aurora_recovery](https://github.com/AuroraRecoveryProject/aurora_recovery): Recovery's final running GUI program
- [curl](https://github.com/AuroraRecoveryProject/curl): A command line tool and library for transferring data with URL syntax, add documentation for how to compile and support in TWRP environment

## Documentation

TWRP Compile Documentation: [TWRP 编译记录](https://github.com/AuroraRecoveryProject/aurora_recovery/docs/TWRP%20%E7%BC%96%E8%AF%91%E8%AE%B0%E5%BD%95.md)