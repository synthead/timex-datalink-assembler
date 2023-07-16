# Docker image for Toebes' WristApp assembler

This repo wraps Toebes' excellent WristApp assembler for the Datalink 150 and 150s in a Docker image with Wine!  Paired
with a helper script called `asm6805`, this Win32 program from 1998 is now presented as a platform-agnostic CLI
experience:

```shell
$ asm6805 01_hello.zsm 01_hello.zap
Assembling 150 Version...
Assembling 150S Version...
Assembly successful: Code=97 Bytes
Successfully copied 2.56kB to /home/max/repos/toebes-datalink-tutorials/tutorials/01_hello.zap
```

## Usage

Here is the usage page for `asm6805`:

```
Usage: asm6805 ASSEMBLY_FILE [OUTPUT_FILE]
Assemble a WristApp or sound scheme for the Timex Datalink 150 and 150s.

ASSEMBLY_FILE is a path to a ZSM assembly file.
OUTPUT_FILE is a path to write the ZAP or SPC file (defaults to out.zap or out.spc).

ZSM format documentation: <https://www.toebes.com/Datalink/wristapps.html>
```

## System requirements

The `asm6805` script will build a Docker image on its first run, and whenever it needs to build an updated image.  Docker
must be installed and running with a connection to the internet to build the image.

If you need to install Docker, you can get it [here](https://docs.docker.com/get-docker).

## Uploading with timex\_datalink\_client

The assembled ZAP files can be synced to your Timex Datalink watch using the
[timex\_datalink\_client Ruby library](https://github.com/synthead/timex_datalink_client) with a Notebook Adapter like
so:

```ruby
require "timex_datalink_client"

models = [
  # For the Timex Datalink 150s, use TimexDatalinkClient::Protocol4 instead.

  TimexDatalinkClient::Protocol3::Sync.new,
  TimexDatalinkClient::Protocol3::Start.new,

  TimexDatalinkClient::Protocol3::WristApp.new(zap_file: "my_wristapp.zap"),

  TimexDatalinkClient::Protocol3::End.new
]

timex_datalink_client = TimexDatalinkClient.new(
  serial_device: "/dev/ttyACM0",
  models: models,
  verbose: true
)

timex_datalink_client.write
```

For more information on how to use timex\_datalink\_client, see the following links:

- [timex\_datalink\_client README](https://github.com/synthead/timex_datalink_client/tree/main#readme)
- [Protocol 3 for the Timex Datalink 150](https://github.com/synthead/timex_datalink_client/blob/main/docs/timex_datalink_protocol_3.md)
- [Protocol 4 for the Timex Datalink 150s](https://github.com/synthead/timex_datalink_client/blob/main/docs/timex_datalink_protocol_4.md)

If you don't have a Timex Notebook Adapter, you can create a 100% compatible one using an Arduino board with
[timex-datalink-arduino](https://github.com/synthead/timex-datalink-arduino).  If you'd like to sync your watch with a
CRT, see [timex\_datalink\_crt](https://github.com/synthead/timex_datalink_crt).

## Toebes' excellent assembler and tutorial

The Dockerfile and `asm6805` wraps the excellent VAsm6805.exe assembler and the very thoughtful header files written by
John A. Toebes, VIII.  Toebes' website contains a wealth of knowledge, excellent tools, great tutorials, and
documentation of inspirational quality.  If you are interested in learning about how to write WristApps for your Timex
Datalink 150 or 150s, you should definitely visit [Toebes' Datalink site](https://toebes.com/Datalink)!
