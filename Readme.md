PLBRAM-Kv260
=======================================================================

This Repository provides example for uiomem and ZynqMP-FPGA-Linux.

## Requirement

 * Board: Kv260
 * OS:
   - ZynqMP-FPGA-Ubuntu22.04-Desktop
     + v1.2.0 https://github.com/ikwzm/ZynqMP-FPGA-Ubuntu22.04-Desktop/tree/v1.2.0
   - ZynqMP-FPGA-Debian12
     + v1.0.0 https://github.com/ikwzm/ZynqMP-FPGA-Debian12/tree/v1.0.0
 * uiomem (v1.0.0-alpha.4) https://github.com/ikwzm/uiomem/tree/v1.0.0-alpha.4
 * fclkcfg (v1.7.3) https://github.com/ikwzm/fclkcfg/tree/v1.7.3

## Boot Kv260 and login fpga user

fpga'password is "fpga".

```console
debian-fpga login: fpga
Password:
fpga@debian-fpga:~$
```

## Download this repository

### Download this repository

```console
fpga@debian-fpga:~$ mkdir work
fpga@debian-fpga:~$ cd work
fpga@debian-fpga:~/work$ git clone https://github.com/ikwzm/PLBRAM-Kv260
Cloning into 'PLBRAM-Kv260'...
remote: Enumerating objects: 30, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (22/22), done.
remote: Total 30 (delta 7), reused 30 (delta 7), pack-reused 0
Unpacking objects: 100% (30/30), done.
fpga@debian-fpga:~/work$ cd PLBRAM-Kv260
```

## Setup

### Build uiomem

```console
fpga@debian-fpga:~/work/PLBRAM-Kv260$ git submodule init
Submodule 'uiomem' (https://github.com/ikwzm/uiomem) registered for path 'uiomem'
fpga@debian-fpga:~/work/PLBRAM-Kv260$ git submodule update
Cloning into '/home/fpga/work/PLBRAM-Kv260/uiomem'...
Submodule path 'uiomem': checked out '960ea869e8b282a5f68123b9a2d7e1f3c52b5f11'
fpga@debian-fpga:~/work/PLBRAM-Kv260$ cd uiomem
fpga@debian-fpga:~/work/PLBRAM-Kv260/uiomem$ make
make -C /lib/modules/6.1.57-zynqmp-fpga-trial/build ARCH=arm64 CROSS_COMPILE= M=/home/fpga/work/PLBRAM-Kv260/uiomem CONFIG_UIOMEM=m modules
make[1]: Entering directory '/usr/src/linux-headers-6.1.57-zynqmp-fpga-trial'
  CC [M]  /home/fpga/work/PLBRAM-Kv260/uiomem/uiomem.o
  MODPOST /home/fpga/work/PLBRAM-Kv260/uiomem/Module.symvers
  CC [M]  /home/fpga/work/PLBRAM-Kv260/uiomem/uiomem.mod.o
  LD [M]  /home/fpga/work/PLBRAM-Kv260/uiomem/uiomem.ko
make[1]: Leaving directory '/usr/src/linux-headers-6.1.57-zynqmp-fpga-trial'
fpga@debian-fpga:~/work/PLBRAM-Kv260/uiomem$ cd ..
```

### Load uiomem

```console
fpga@debian-fpga:~/work/PLBRAM-Kv260$ sudo insmod uiomem/uiomem.ko
```

### Load FPGA and Device Tree

```console
fpga@debian-fpga:~/work/PLBRAM-Kv260$ sudo rake install
gzip -d -f -c plbram_256k_dbg.bin.gz > /lib/firmware/plbram_256k_dbg.bin
./dtbo-config --install plbram_256k --dts plbram_256k_dbg.dts
<stdin>:35.18-39.20: Warning (unit_address_vs_reg): /fragment@2/__overlay__/uiomem_plbram: node has a reg or ranges property, but no unit name
<stdin>:26.13-41.5: Warning (avoid_unnecessary_addr_size): /fragment@2: unnecessary #address-cells/#size-cells without "ranges" or child "reg" property
```

```console
fpga@debian-fpga:~/work/PLBRAM-Kv260$ dmesg | tail -16
[ 3045.853809] fpga_manager fpga0: writing plbram_256k_dbg.bin to Xilinx ZynqMP FPGA Manager
[ 3045.992810] OF: overlay: WARNING: memory leak will occur if overlay removed, property: /fpga-full/firmware-name
[ 3046.008892] fclkcfg amba_pl@0:fclk0: driver version : 1.7.3
[ 3046.008910] fclkcfg amba_pl@0:fclk0: device name    : amba_pl@0:fclk0
[ 3046.008915] fclkcfg amba_pl@0:fclk0: clock  name    : pl0_ref
[ 3046.008920] fclkcfg amba_pl@0:fclk0: clock  rate    : 99999999
[ 3046.008946] fclkcfg amba_pl@0:fclk0: clock  enabled : 1
[ 3046.008951] fclkcfg amba_pl@0:fclk0: remove rate    : 1000000
[ 3046.008957] fclkcfg amba_pl@0:fclk0: remove enable  : 0
[ 3046.008962] fclkcfg amba_pl@0:fclk0: driver installed.
[ 3046.009862] uiomem uiomem0: driver version = 1.0.0-alpha.4
[ 3046.009876] uiomem uiomem0: major number   = 237
[ 3046.009882] uiomem uiomem0: minor number   = 0
[ 3046.009887] uiomem uiomem0: range address  = 0x0000000400000000
[ 3046.009893] uiomem uiomem0: range size     = 262144
[ 3046.009899] uiomem 400000000.uiomem_plbram: driver installed.
```

### Build plbram_test

```console
fpga@debian-fpga:~/work/PLBRAM-Kv260$ rake plbram_test
gcc  -o plbram_test plbram_test.c
```

## Run plbram_test

```console
fpga@debian-fpga:~/examples/PLBRAM-Ultra96$ ./plbram_test
ize=262144
mmap write test : sync=1 time=0.000570 sec (0.000569 sec)
mmap read  test : sync=1 time=0.002787 sec (0.002786 sec)
compare = ok
mmap write test : sync=0 time=0.000447 sec (0.000245 sec)
mmap read  test : sync=1 time=0.002847 sec (0.002847 sec)
compare = ok
mmap write test : sync=1 time=0.000545 sec (0.000545 sec)
mmap read  test : sync=0 time=0.000346 sec (0.000296 sec)
compare = ok
mmap write test : sync=0 time=0.000426 sec (0.000216 sec)
mmap read  test : sync=0 time=0.000335 sec (0.000297 sec)
compare = ok
file write test : sync=1 time=0.000267 sec (0.000266 sec)
mmap read  test : sync=0 time=0.000337 sec (0.000296 sec)
compare = ok
file write test : sync=0 time=0.000289 sec (0.000266 sec)
mmap read  test : sync=0 time=0.000336 sec (0.000297 sec)
compare = ok
mmap write test : sync=0 time=0.000397 sec (0.000198 sec)
file read  test : sync=1 time=0.000398 sec (0.000398 sec)
compare = ok
mmap write test : sync=0 time=0.000442 sec (0.000232 sec)
file read  test : sync=0 time=0.000368 sec (0.000331 sec)
compare = ok
```

## Clean up

```console
fpga@debian-fpga:~/work/PLBRAM-Kv260$ sudo rake uninstall
./dtbo-config --remove plbram_256k
```

```console
fpga@debian-fpga:~/work/PLBRAM-Kv260$ dmesg | tail -2
[ 3402.803798] uiomem 400000000.uiomem_plbram: driver removed.
[ 3402.806845] fclkcfg amba_pl@0:fclk0: driver removed.
```

## Build Bitstream file

### Requirement

* Vivado 2023.1

### Download this repository

```console
shell$ git clone https://github.com/ikwzm/PLBRAM-Kv260
Cloning into 'PLBRAM-Kv260'...
remote: Enumerating objects: 30, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (22/22), done.
remote: Total 30 (delta 7), reused 30 (delta 7), pack-reused 0
Unpacking objects: 100% (30/30), done.
```

### Create Vivado Project

```console
vivado% cd fpga
vivado% vivado -mode batch -source create_project.tcl
vivado% cd ..
```

or

```
Vivado > Tools > Run Tcl Script... > fpga/create_project.tcl
```

### Implementation

```console
vivado% cd fpga
vivado% vivado -mode batch -source implementation.tcl
vivado% cd ..
```

or

```
Vivado > Tools > Run Tcl Script... > fpga/implementation.tcl
```

### Convert from Bitstream File to Binary File

```console
vivado% cd fpga
vivado% bootgen -image plbram_256k_dbg.bif -arch zynqmp -w -o ../plbram_256k_dbg.bin
vivado% cd ..
```

### Compress plbram_256k_dbg.bin to plbram_256k_dbg.gz

```console
vivado% gzip plbram_256k_dbg.bin
```

