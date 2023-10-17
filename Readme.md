PLBRAM-Kv260
=======================================================================

This Repository provides example for uiomem and ZynqMP-FPGA-Linux.

# Build Bitstream file

## Requirement

* Vivado 2023.1

## Download this repository

```console
shell$ git clone https://github.com/ikwzm/PLBRAM-Kv260
Cloning into 'PLBRAM-Kv260'...
remote: Enumerating objects: 30, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (22/22), done.
remote: Total 30 (delta 7), reused 30 (delta 7), pack-reused 0
Unpacking objects: 100% (30/30), done.
```

## Create Vivado Project

```console
vivado% cd fpga
vivado% vivado -mode batch -source create_project.tcl
vivado% cd ..
```

or

```
Vivado > Tools > Run Tcl Script... > fpga/create_project.tcl
```

## Implementation

```console
vivado% cd fpga
vivado% vivado -mode batch -source implementation.tcl
vivado% cd ..
```

or

```
Vivado > Tools > Run Tcl Script... > fpga/implementation.tcl
```

#### Convert from Bitstream File to Binary File

```console
vivado% cd fpga
vivado% bootgen -image plbram_256k_dbg.bif -arch zynqmp -w -o ../plbram_256k_dbg.bin
vivado% cd ..
```

#### Compress plbram_256k_dbg.bin to plbram_256k_dbg.gz

```console
vivado% gzip plbram_256k_dbg.bin
```

