# The BUS format specification

The BUS format is a binary format for storing intermediate results for single cell RNA-Seq datasets. This repository details the specification of the format.

The motivation and example usage of the BUS format is described in

P Melsted, V Ntranos, L Pachter, [The Barcode, UMI, Set format and BUStools](https://academic.oup.com/bioinformatics/advance-article/doi/10.1093/bioinformatics/btz279/5487510), Bioinformatics, btz279, 2019.


### Tools

#### BUS generation

- [kallisto](https://pachterlab.github.io/kallisto) version 0.45.0 and later

#### BUS file manipulation

- [bustools](https://github.com/BUStools/bustools)

#### BUS parsing

- BUS [R notebooks](https://github.com/BUStools/BUS_notebooks_R) and [python notebooks](https://github.com/BUStools/BUS_notebooks_python)

### Format specification

A BUS file is a binary file consisting of a header followed by zero or more BUS records. Each BUS header consists of the following elements in order

|Field name | Description | Type | Value |
|-----------|-------------|------|-------|
| magic | fixed magic string | char[4] | BUS\0 |
| version | BUS format version | uint32_t | |
| bc_len | Barcode length [1-32] | uint32_t | |
| umi_len | UMI length [1-32] | uint32_t | |
| tlen   | Length of plain text header | uint32_t | |
| text | Plain text header | char[tlen] |  |

The encoding used for the text header is ASCII.

BUS records are stored directly after the header in the following format, the size of each BUS record is rounded up to 32 bytes. This is done by adding 32 unused bits at the end of the record.

|Field name | Description | Type |
|-----------|-------------|------|
|barcode | 2-bit encoded barcode | uint64_t |
|umi | 2-bit encoded UMI | uint64_t |
|ec | equivalence class | int32_t |
| count| fragment count |   uint32_t
| flags| flags |   uint32_t |

The flags column can be used to store extra information, but the format specification of BUS files does not put any restrictions or specify the content. 

The 2-bit encoding encodes `A,C,G,T` and `00,01,10,11`, and such that the first nucleotides are encoded in the most significant bits. For example the barcode `GCCA` corresponds to the bit code `10010100` or the integer `148`. All integers are encoded as little-endian. 
