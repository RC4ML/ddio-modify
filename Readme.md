# Enable/Disable/Tune DDIO

## what is DDIO?

- please read [this repo](https://github.com/aliireza/ddio-bench)
  and [this paper](https://www.usenix.org/conference/atc20/presentation/farshin)

## How to Modify setting?

Each platform has its own setting policy, following is our platform

|      | CPU                    | MotherBoard   | Memory      |
|------|------------------------|---------------|-------------|
| Name | Intel Xeon Silver 4214 | X11DPG-OT-CPU | DDR4 2400*8 |

### Get DDIO setting

1. install `msr-tools`, e.g.(debian) `sudo apt install msr-tools`
2. enable linux kernel module `msr`, e.g. `sudo modprobe msr`
3. use `sudo rdmsr -a 0xc8b` to get current setting

You will get a num like `600` and regard it as bitmask to allocate LLC ways.

### Tune DDIO setting

1. use `sudo wrmsr -a 0xc8b 0x600` to set, `0x600` is the value you get from `rdmsr` command, range
   from `0x600 to 0x7ff`(LLC n-ways associations)

### Disable DDIO

Check ddio-modify.cpp to see details

```bash
cd /path/to/repo && mkdir build && cd build && cmake .. && make
sudo ./ddio_modify --enable=false --nic_bus=0x3a
```  

### Enalbe DDIO

```bash
#same as disable, but use following command
sudo ./ddio_modify --enable=true --nic_bus=0x3a
```

### What is `--nic_bus`?

According
to [this document](https://cdrdv2-public.intel.com/614073/614073_Intel%C2%AE%20Xeon%C2%AE%20Processor%20Scalable%20Fam_v005.pdf)
, we need set proper value to `PERFCTRLSTS_0` field, which belong to a root bus device. So we need set `--nic_bus` to
the bus number of the root bus device.

As we know, PCIE is a tree structure, and the root bus device belongs to the children of root. So we can
use `lspci -v -t` to check which root bus device is the grandparent of target NIC.

On our platform, this target root bus device is `0x3a`. 