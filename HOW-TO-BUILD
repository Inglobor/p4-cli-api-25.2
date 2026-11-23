# What is being built

The source can be used to build two components:
- `p4` CLI binary itself
- `Perforce API` package for static linkage

Both components require OpenSSL. API package build provided via GitHub release is built against OpenSSL 3.5.9. The OpenSSL used for that build is provided as part of the release as well, to streamline linking with the API package. 

There may be additional platform-specific dependencies when linking with the API package (as noted below for CLI build).

# How to build

## Linux

Tested on WSL/Ubuntu 26.04

### Preparations

1. Get `jam`:
- `sudo apt update`
- `sudo apt install jam`

2. Install compiler tools
- `sudo apt install -y build-essential`

3. Enable Linux file metadata in WSL
- Open `/etc/wsl.conf` and add
```
[automount]
options = "metadata"
```
- Restart WSL

4. Get `git` if necessary

5. Clone the repository - `git clone https://github.com/Inglobor/p4-cli-api-25.2.git ~/code/`

### To build `OpenSSL`

1. Get OpenSSL source & switch to 3.5.9
- `git clone https://github.com/openssl/openssl.git ~/code/openssl`
- `cd ~/code/openssl/`
- `git checkout tags/openssl-3.5.9 -b build-3.5.9`

2. Build the OpenSSL
- `./Configure linux-x86_64 --prefix=/tmp/openssl-x86`
- `make -j$(nproc)`
- `make install_sw`

3. Create OpenSSL artifact for GitHub release
- `mkdir -p ~/code/openssl-package/lib`
- `mkdir -p ~/code/openssl-package/include`
- `cd ~/code/openssl-package/`
- `cp /tmp/openssl-x86/lib64/*.a ./lib`
- `cp -R ~/code/openssl/include/* .`
- `tar -a -cf openssl_3.5.9-static-linux-x86_64.tar.gz *`

### To build `Perforce API`

1. Go to source dir - `cd /code/p4-cli-api-25.2/src`

2. Run `jam -sOSVER=26 -sGCCVER=11.4.0 -sSMARTHEAP=0 -sTYPE=pic -sMALLOC_OVERRIDE=0 -sUSE_EXTENSIONS=0 -sSSLLIBDIR=/tmp/openssl-x86/lib64/ -sSSLINCDIR=/tmp/openssl-x86/include -sPRODUCTION=1 p4api.tar` for Release build

3. Run `jam -sOSVER=26 -sGCCVER=11.4.0 -sSMARTHEAP=0 -sTYPE=gpic -sMALLOC_OVERRIDE=0 -sUSE_EXTENSIONS=0 -sSSLLIBDIR=/tmp/openssl-x86/lib64/ -sSSLINCDIR=/tmp/openssl-x86/include -sPRODUCTION=1 p4api.tar` for Debug build

4. Create P4 artifact for GitHub release
- `cd ..`
- `mkdir -p artifact/lib/release`
- `mkdir -p artifact/lib/debug`
- `mkdir -p artifact/include`
- `cd artifact`
- `cp p4-bin/bin.linux26x86_64/pic/*.a artifact/lib/release`
- `cp p4-bin/bin.linux26x86_64/gpic/*.a artifact/lib/debug`
- `cp -R p4-bin/bin.linux26x86_64/gpic/p4api-2025.2.3086021/include/ artifact/`
- `tar -a -cf p4api-2025.2.3086021-static-linux26x86_64.tar.gz *`

### To build `P4 CLI`

1. Install additional link-time dependencies
- `sudo apt install -y libjitterentropy3-dev`
- `sudo apt install -y libzstd-dev`
- `sudo apt install -y zlib1g-dev`

2. Run `jam -sOSVER=26 -sGCCVER=11.4.0 -sSMARTHEAP=0 -sTYPE=pic -sMALLOC_OVERRIDE=0 -sUSE_EXTENSIONS=0 -sSSLLIBDIR=/tmp/openssl-x86/lib64/ -sSSLINCDIR=/tmp/openssl-x86/include p4` for Release build

## Mac

Tested on Mac 12.7

### Preparations

1. Get `command line developer tools` to get `git` etc. - start e.g. by running `git` in terminal

2. Install `MacPorts` or `Homebrew`

3. Install `jam`
- Use `brew install jam` or `sudo port install jam` etc.

4. Clone the repository - `git clone https://github.com/Inglobor/p4-cli-api-25.2.git ~/code/`

### To build `OpenSSL` (universal)

1. Get OpenSSL source & switch to 3.5.9
- `git clone https://github.com/openssl/openssl.git ~/code/openssl`
- `cd ~/code/openssl/`
- `git checkout tags/openssl-3.5.9 -b build-3.5.9`

2. Configure OpenSSL Intel platform build (`./Configure darwin64-x86_64-cc --prefix=/tmp/openssl-x86 no-asm`) and build it (`make clean && make && make install`)

3. Configure OpenSSL Apple platform build (`./Configure darwin64-arm64-cc --prefix=/tmp/openssl-arm no-asm`) and build it (`make clean && make && make install`)

4. Merge both versions to universal build
- `mkdir -p /tmp/openssl-universal/lib`
- Merge `libcrypto` - `lipo -create /tmp/openssl-x86/lib/libcrypto.a /tmp/openssl-arm/lib/libcrypto.a -output /tmp/openssl-universal/lib/libcrypto.a`
- Merge `libssl` - `lipo -create /tmp/openssl-x86/lib/libssl.a /tmp/openssl-arm/lib/libssl.a -output /tmp/openssl-universal/lib/libssl.a`
- Copy includes - `cp -R /tmp/openssl-arm/include /tmp/openssl-universal/include`

5. Create OpenSSL artifact for GitHub release
- `cd /tmp/openssl-universal/`
- `tar -a -cf openssl-3.5.9-static-macosx12u.tar.gz *`

### To build `Perforce API`

1. Go to source dir - `cd /code/p4-cli-api-25.2/src`

2. Run `jam -sOSVER=120 -sCLANGVER=14.0.0 -sTYPE=pic -sUSE_EXTENSIONS=0 -sOSPLAT=U -sPRODUCTION=1 -sSSLLIBDIR=/tmp/openssl-universal/lib/ -sSSLINCDIR=/tmp/openssl-universal/include/ p4api.tar` for Release build

3. Run `jam -sOSVER=120 -sCLANGVER=14.0.0 -sTYPE=gpic -sUSE_EXTENSIONS=0 -sOSPLAT=U -sPRODUCTION=1 -sSSLLIBDIR=/tmp/openssl-universal/lib/ -sSSLINCDIR=/tmp/openssl-universal/include/ p4api.tar` for Debug build

4. Create P4 artifact for GitHub release
- `cd ..`
- `mkdir -p artifact/lib/release`
- `mkdir -p artifact/lib/debug`
- `mkdir -p artifact/include`
- `cp p4-bin/bin.macosx120u/pic/*.a artifact/lib/release`
- `cp p4-bin/bin.macosx120u/gpic/*.a artifact/lib/debug`
- `cp -R p4-bin/bin.macosx120u/gpic/p4api-2025.2.3086021/include/ artifact/include/`
- `cd artifact`
- `tar -a -cf p4api-2025.2.3086021-static-macosx120u.tar.gz *`

### To build `P4 CLI`

1. Run `jam -sOSVER=120 -sCLANGVER=14.0.0 -sTYPE=pic -sUSE_EXTENSIONS=0 -sOSPLAT=U -sSSLLIBDIR=/tmp/openssl-universal/lib/ -sSSLINCDIR=/tmp/openssl-universal/include/ p4` for Release build

## Windows

Tested on Windows 11 with VS 2026 toolchain

### Preparations

1. Setup console for MSVC environment, e.g. `c:\Program Files\Microsoft Visual Studio\18\Insiders\VC\Auxiliary\Build\vcvars64.bat`

2. Get `jam` - if you can't find precompiled version, you can build it from source:
- Download source from `https://swarm.workshop.perforce.com/files/guest/perforce_software/jam/src` and unpack to local folder, e.g. `C:\code\jam`
- Run `cl /nologo /DNT /D_WIN32 /Fejam.exe *.c advapi32.lib` in the folder (from console with MSVC environment). If you're seeing link issues, you may need to remove `mkjambase.c` from the folder
- Make sure `jam` binary is in PATH

3. Get `perl` (e.g. _Strawberry_) and add it to PATH

4. Get `nasm` and add it to PATH

5. Get `git` and add it to PATH

### To build `OpenSSL`

1. Get OpenSSL source & switch to 3.5.9
- `git clone https://github.com/openssl/openssl.git C:\code\openssl`
- `cd C:\code\openssl`
- `git checkout tags/openssl-3.5.9 -b build-3.5.9`

2. Build OpenSSL in Release configuration
- Configure - `perl Configure VC-WIN64A no-shared`
- Build it - `nmake`
- Install it - `nmake install`

3. Take relevant files from installation folder to artifact for GitHub release
- `mkdir C:\code\openssl\artifact\lib\Release`
- `copy "C:\Program Files\OpenSSL\lib\*.lib" C:\code\openssl\artifact\lib\Release`
- `xcopy /EIY "C:\Program Files\OpenSSL\include" C:\code\openssl\artifact\include`

4. Build OpenSSL in Debug configuration
- Clean previous build - `nmake distclean`
- Configure - `perl Configure debug-VC-WIN64A no-shared`
- Build it - `nmake`
- Install it - `nmake install`

5. Take relevant files from installation folder to artifact for GitHub release
- `mkdir C:\code\openssl\artifact\lib\Debug`
- `copy "C:\Program Files\OpenSSL\lib\*.lib" C:\code\openssl\artifact\lib\Debug`

6. Finish the GitHub release artifact
- `cd C:\code\openssl`
- `tar -z -cf openssl-3.5.9-static-windows-x86_64.tar.gz -C artifact *`

### To build `Perforce API`

1. Go to source dir - `cd C:\code\p4-cli-api-25.2\src`

2. Run `jam -sMSVSVER=18 -sSMARTHEAP=0 -sTYPE=pic -sMALLOC_OVERRIDE=0 -sUSE_EXTENSIONS=0 -sUSE_WILDARGS=no -sSSLLIBDIR="C:\code\OpenSSL\artifact\lib" -sSSLINCDIR="C:\code\OpenSSL\artifact\include" -sPRODUCTION=1 p4api.tar` for Release build

3. Run `jam -sMSVSVER=18 -sSMARTHEAP=0 -sTYPE=picg -sMALLOC_OVERRIDE=0 -sUSE_EXTENSIONS=0 -sUSE_WILDARGS=no -sSSLLIBDIR="C:\code\OpenSSL\artifact\lib" -sSSLINCDIR="C:\code\OpenSSL\artifact\include" -sPRODUCTION=1 p4api.tar` for Debug build

4. Create P4 artifact for GitHub release
- `cd ..`
- `mkdir artifact\lib\release`
- `mkdir artifact\lib\debug`
- `mkdir artifact\include`
- `copy p4-bin\bin.ntx64\pic\*.lib artifact\lib\release`
- `copy p4-bin\bin.ntx64\picg\*.lib artifact\lib\debug`
- `xcopy /EIY p4-bin\bin.ntx64\pic\p4api-2025.2.3086021-static_pic\include\ artifact\include`
- `tar -z -cf p4api-2025.2.3086021-static-windows-x86_64.tar.gz -C artifact *`

### To build `P4 CLI`

1. Run `jam -sMSVSVER=18 -sSMARTHEAP=0 -sTYPE=pic -sMALLOC_OVERRIDE=0 -sUSE_EXTENSIONS=0 -sUSE_WILDARGS=no -sSSLLIBDIR="C:\code\OpenSSL\artifact\lib" -sSSLINCDIR="C:\code\OpenSSL\artifact\include" p4` for Release build
