# KRaBs: Kernel Reader and Booters
KRaBs is an x86/x86_64 chain loader written in pure Rust.  

## What is KRaBs?
KRaBs is working on booting vmlinux and other kernels formatted in ELF on 32-bit/64-bit PCs and is under the development. Krabs also aims to support only the minimal Linux x86/x86_64 boot protocol. This allows you to use the kernel command line and initrd/initramfs.  

Other features:
* Supports GPT and FAT32 File System on EFI System Partition(ESP).
* You can configure KRaBs's boot option in CONFIG.TXT on FAT32 ESP.
* CONFIG.TXT is a simple matrix-oriented text file. See [CONFIG.TXT formats](#configtxt-format).

## News
* 2020/08: **Currently, KRaBs can boot kernel-5.8.3! initrd and kernel command line also work fine!!**. see [details](docs/linux-image-setup-64.md)
   ```
   cargo run -- -we disk.img
   ```

![sample](./docs/images/demo3.gif)

## Getting Started
To get started with KRaBs, build it from source.

### Requirements
1. `rust-src` and `llvm-tools-preview`
2. GPTed disk image that has BIOS Boot Partition and EFI System Partition.
3. CONFIG.TXT, kernel image and initrd on FAT32 FileSystem on EFI System Partition.
4. (option) qemu-system-x86

#### Prepare `rust-src` and `llvm-tools-preview`
```shell
$ cd /path/to/krabs
$ rustup component add rust-src
$ rustup component add llvm-tools-preview
```

#### Example of GPT disk image:
```shell
$ gdisk -l disk.img 
...
Found valid GPT with protective MBR; using GPT.
Disk disk2.img: 204800 sectors, 100.0 MiB
Sector size (logical): 512 bytes
Disk identifier (GUID): 2A1F86BB-74EA-47C5-923A-7A3BAF83B5DF
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 204766
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048            4095   1024.0 KiB  EF02  BIOS boot partition
   2            4096          106495   50.0 MiB    EF00  EFI system partition
   3          106496          204766   48.0 MiB    8300  Linux filesystem
$ sudo kpartx -av disk.img
$ sudo mkfs.fat -F 32 /dev/mapper/loop0p2
$ sudo mkfs.ext4 /dev/mapper/loop0p3
```

#### Prepare CONFIG.TXT, kernel, initrd:
```shell
$ sudo mount /dev/mapper/loop0p2 /mnt
$ ls /mnt
CONFIG.TXT  initramfs.cpio.gz vmlinux-5.8.3
```

#### CONFIG.TXT format
simple matrix-oriented text file
```shell
main.kernel vmlinux-5.8.3
main.initrd initramfs.cpio.gz
main.cmdlin clocksource=tsc net.ifnames=0
```

### Build
```shell
cargo build
```

### Burn
```shell
cargo run -- -w disk.img
```

### Test
```shell
cargo run -- -e disk.img
```

## Examples 
Examples for x86-64 Linux is described in
[the docs of 'Creating Custom Linux Images and Booting'](docs/linux-image-setup-64.md).

## Contributing
KRaBs welcomes all contributions.
To contribute to KRaBs, check out the [getting started guide](#getting-started)
and then the KRaBs [contribution guidelines](CONTRIBUTING.md).

## Design
KRaBs's overall architecture is described in
[the design document](docs/design.md) and
[the specification document](docs/specifications.md).

## Features
1. Supports legacy BIOS.
2. Supported media are HDD and SSD which have GPT.
3. Supports 32bit protected mode and 64bit long mode. 
4. Supports OS kernel formatted in ELF32/ELF64.
5. Supports minimal
[x86/x86_64 linux boot protocol](https://www.kernel.org/doc/html/latest/x86/boot.html). 
6. KRaBs interprets the FAT32 file system.
7. KRaBs's boot optuon is configureset by CONFIG.TXT on the FAT32 file system.
7. KRaBs can load modules such as initramsfs/initrd according to linux boot protocol.
8. KRaBs can transmit kernel command line to the kernel according to linux boot protocol.

## ToDO
1. support gziped kernel.
2. support recovery mode.

## License
This project is licensed under either of

* Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or
   http://www.apache.org/licenses/LICENSE-2.0)
* MIT license ([LICENSE-MIT](LICENSE-MIT) or
   http://opensource.org/licenses/MIT)

at your option.

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in Serde by you, as defined in the Apache-2.0 license, shall be
dual licensed as above, without any additional terms or conditions.

## Contact
You can get in touch with me in the following ways:

* Contact me on my [twitter](https://twitter.com/o8_vm).
* Open a GitHub issue in this repository.
* Email me at [o8@vmm.dev](mailto:o8@vmm.dev).

_Note: I'm on a Japan time zone._  

When communicating within the Krabs community, please mind our
[code of conduct](CODE_OF_CONDUCT.md).
