# get-fedora-coreos

`get-fedora-coreos` is a simple POSIX shell script to download [Fedora CoreOS][coreos]
images. It automatically validates checksums and GPG signatures.

It's useful for scripting alongside [Packer][packer]. For example, when using the [Qemu
Packer Plugin][qemu_packer_plugin] you can:

- Set `iso_url` to the path to your downloaded image.
- Set `iso_checksum` to the contents of the `sha256` file created for each image.

[coreos]: https://fedoraproject.org/coreos/
[packer]: https://www.packer.io/
[qemu_packer_plugin]: https://developer.hashicorp.com/packer/integrations/hashicorp/qemu/latest/components/builder/qemu

## Installation

```console
curl --proto =https --tlsv1.2 -sSLfO \
  https://raw.githubusercontent.com/jamielinux/get-fedora-coreos/main/get-fedora-coreos
chmod +x get-fedora-coreos
sudo cp get-fedora-coreos /usr/local/bin
```

### Dependencies

- `curl`
- `gpgv`
- `jq`
- `sha256sum`

## Usage

```console
Usage: get-fedora-coreos list-available [STREAM]
   or: get-fedora-coreos check-latest [STREAM]
   or: get-fedora-coreos download STREAM VERSION ARCHITECTURE IMAGE_TYPE IMAGE_FORMAT
   or: get-fedora-coreos list-downloads
```

To see the latest available `VERSION`:

```console
$ get-fedora-coreos check-latest
39.20240322.3.1

$ get-fedora-coreos check-latest testing
39.20240407.2.0
```

To see available values for `ARCHITECTURE`, `IMAGE_TYPE` and `IMAGE_FORMAT`:

```console
$ get-fedora-coreos list-available
aarch64 aws vmdk.xz
...
x86_64 qemu qcow2.xz
x86_64 virtualbox ova
x86_64 vmware ova
```

To download the latest `VERSION`:

```console
$ get-fedora-coreos download stable latest x86_64 qemu qcow2.xz
==> Latest version is: 39.20240322.3.1

==> Downloading meta.json
############################################################################# 100.0%
==> Downloading fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz
############################################################################# 100.0%
==> Downloading fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz.sig
############################################################################# 100.0%
==> Downloading fedora.gpg
############################################################################# 100.0%

==> Validating checksum
fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz: OK

==> Validating GPG signature
gpgv: Signature made Tue 09 Apr 2024 19:53:52 BST
gpgv:                using RSA key E8F23996F23218640CB44CBE75CF5AC418B8E74C
gpgv: Good signature from "Fedora (39) <fedora-39-primary@fedoraproject.org>"

==> Success! Your Fedora CoreOS image is available here:
/home/user/.cache/get-fedora-coreos/stable/39.20240322.3.1/x86_64/qemu/fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz
```

To download a specific `VERSION`:

```console
$ get-fedora-coreos download stable 39.20240322 x86_64 qemu qcow2.xz
```

To list images you've downloaded:

```console
$ get-fedora-coreos list-downloads
/home/user/.cache/get-fedora-coreos/next/40.20240416.1.0/x86_64/qemu/fedora-coreos-40.20240416.1.0-qemu.x86_64.qcow2.xz
/home/user/.cache/get-fedora-coreos/stable/39.20240322.3.1/x86_64/qemu/fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz
```

## Directory contents

For each image you download, you end up with a directory with these contents:

```console
$ ls -1 ~/.cache/get-fedora-coreos/stable/39.20240322.3.1/x86_64/qemu
fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz
fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz-CHECKSUM
fedora-coreos-39.20240322.3.1-qemu.x86_64.qcow2.xz.sig
fedora.gpg
meta.json
sha256
sha256-uncompressed
```

There won't be a `sha256-uncompressed` file if the image isn't compressed.

## Environment variables

- `$GET_COREOS_CACHE`
  - The directory where downloaded files will be stored.
  - Default: `$XDG_CACHE_HOME/get-fedora-coreos`
  - Fallback: `~/.cache/get-fedora-coreos`
- `$NO_COLOR`
  - If this is set to a non-empty value, output won't be colorized.
