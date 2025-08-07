# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

IOSCSITape is a Unix-like tape driver kernel extension (kext) for Mac OS X that enables standard utilities like `tar` and `dd` to work with SCSI tape drives. It includes:
- A kernel extension (IOSCSITape.kext) that provides character device files (/dev/rst0, etc.)
- A command-line tool (`mt`) for tape drive manipulation

## Build Configuration for Mac OS X 10.4 Tiger (Xcode 2.5)

The project has been configured to build on Mac OS X 10.4.11 (Tiger) with Xcode 2.5. Key settings:
- objectVersion = 42 (Xcode 2.5 compatible)
- SDK: /Developer/SDKs/MacOSX10.4u.sdk
- Minimum OS Version: MAC_OS_X_VERSION_MIN_REQUIRED = 1040
- Architectures: ppc and i386 (Universal Binary)

## Build Commands

### Using Xcode 2.5 GUI
1. Open IOSCSITape.xcodeproj in Xcode 2.5
2. Select Build Configuration (Debug or Release)
3. Build All (Cmd+B)

### Using xcodebuild command line
```bash
# Build both targets (kext and mt tool)
xcodebuild -configuration Release

# Build specific target
xcodebuild -target IOSCSITape -configuration Release
xcodebuild -target mt -configuration Release

# Clean build
xcodebuild clean
```

## Architecture

### Kernel Extension (IOSCSITape.kext)
- **IOSCSITape.cpp/h**: Main driver class inheriting from IOSCSIPrimaryCommandsDevice
- Implements SCSI Stream Commands (SSC) for tape operations
- Provides BSD character device interface through cdevsw callbacks

### mt Tool
- **mt.c**: Command-line utility for tape operations (rewind, forward space, etc.)
- **custom_mtio.h**: Additional ioctl definitions for tape operations
- **mtio.h**: Standard magnetic tape I/O definitions (from 10.5 SDK)

### Key Components
- Character device operations: st_open, st_close, st_readwrite, st_ioctl
- SCSI command implementation for tape-specific operations (REWIND, SPACE, WRITE_FILEMARKS, etc.)
- BSD-IOKit data exchange through IOMemoryDescriptor

## Testing

After building:
1. Load the kext: `sudo kextload build/Release/IOSCSITape.kext`
2. Check for device files: `ls -la /dev/rst*`
3. Use mt tool: `./build/Release/mt -f /dev/rst0 status`

## Important Notes

- This driver is experimental and not intended for production use
- Requires SCSI tape hardware and appropriate SCSI adapter
- Must be loaded with appropriate permissions (typically requires sudo)
- Device files are created automatically when tape drives are detected