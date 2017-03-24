# VSM star

[Versity Storage Manager](http://www.versity.com)

Versity Storage Manager archive format is based on the SAM-QFS archive format that is a standard tar format with a few minor changes for supporting large files.

The tar file size field in the tar header is only capable of supporting 12 octal digits and therefore overflows for files larger than 68719476735 bytes (roughly 64GB).  Modern versions of tar support large file sizes in a different way than the VSM/SAM-QFS format does, and is therefore incompatible with standard releases of tar.

For small files, the standard tar utility work fine for VSM archive files.  For support of the larger files, the star utility contained in this repo (or shipped with VSM release) is required.

```sh
# sls -D
100M:
  mode: -rw-r--r--  links:   1  owner: root      group: root    
  length: 104857600  admin id:      0  inode:      132.1
  archdone;
  copy 1: ------ Mar 24 20:05         1.1    dk dkar01 f1
  access:        Mar 24 20:03  modification: Mar 24 20:03
  changed:       Mar 24 20:03  attributes:   Mar 24 20:03
  creation:      Mar 24 20:03  residence:    Mar 24 20:03

70G:
  mode: -rw-r--r--  links:   1  owner: root      group: root    
  length: 75161927680  admin id:      0  inode:      131.1
  archdone;
  copy 1: ------ Mar 24 19:48         0.1    dk dkar01 f0
  access:        Mar 24 19:48  modification: Mar 24 19:40
  changed:       Mar 24 19:40  attributes:   Mar 24 19:38
  creation:      Mar 24 19:38  residence:    Mar 24 19:38
```

In this example, the 100M file is readable by the standard tar utility:
```sh
# tar -tvf /mnt/qfs3/dkar01/f1 
-rw-r--r-- root/root 104857600 2017-03-24 20:03 100M
```
However, the 70G file gives the following error:
```sh
# tar -tvf /mnt/qfs3/dkar01/f0 
tar: Archive contains `x01180000000' where numeric off_t value expected
-rw-r--r-- root/root 18446744073709551615 2017-03-24 19:40 70G
```

To build the star utility, run the following:
```sh
# ./configure && make
```

The resulting binary is able to read the large file tar:
```sh
# ./src/tar -tvf /mnt/qfs3/dkar01/f0
-rw-r--r-- root/root 75161927680 2017-03-24 19:40 70G
```

See the included README for the details of the base tar package.

