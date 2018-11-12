# scedtools
Collection of tools for automated processing and clustering of single-crystal electron diffraction data

## Pipeline tools

### autoindex.py

Looks for files matching `XDS.INP` in all subdirectories and runs them using `XDS`.

	In:  XDS.INP
	Out: XDS data processing on all files

### extract_xds_info.py

Looks files matching `CORRECT.LP` in all subdirectories and extracts unit cell/integration info. Summarizes the unit cells in the excel file `cells.xlsx` and `cells.yaml`. XDS_ASCII.HKL files matching the completeness / CC(1/2) criteria are listed in `filelist.txt`. Optionally, gathers the corresponding `XDS_ASCII.HKL` files in the local directory. The `cells.yaml` file can be used as input for further processing.

	In:  CORRECT.LP
	Out: cells.yaml
	     cells.xlsx
	     filelist.txt

### find_cell.py

This program a cells.yaml file and shows histogram plots with the unit cell parameters. This program mimicks `CELLPARM` (http://xds.mpimf-heidelberg.mpg.de/html_doc/cellparm_program.html) and calculates the weighted mean lattice parameters, where the weight is typically the number of observed reflections (defaults to 1.0). For each lattice parameter, the mean is calculated in a given range (default range = median+-2). The range can be changed by dragging the cursor on the histogram plots. 

Alternatively, the unit cells can be clustered by giving the `--cluster` command, in which a dendrogram is shown. The cluster cutoff can be selected by clicking in the dendrogram. The clusters will be written to `cells_cluster_#.yaml`.

	In:  cells.yaml
	Out: mean cell parameters
	     cells_*.yaml (clustering only)

### make_xscale.py

Prepares an input file `XSCALE.INP` for `XSCALE` and corresponding `XDSCONV.INP` for `XDSCONV`. Takes a `cells.yaml` file or a series of `XDS_ASCII.HKL` files as input, and uses those to generate the `XSCALE.INP` file.

	In:  cells.yaml / XDS_ASCII.HKL
	Out: XSCALE.INP

### cluster.py

Parses the `XSCALE.LP` file for the correlation coefficients between reflection files to perform hierarchical cluster analysis (Giordano et al., Acta Cryst. (2012). D68, 649–658). The cutoff threshold can be selected by clicking in the dendrogram window. The program will write new `XSCALE.LP` files to subdirectories `cluster_#`, and run `XSCALE` on them, and (if available), pointless.

	In:  XSCALE.LP
	Out: cluster_n/
		filelist.txt
		*_XDS_ASCII.HKL
		XSCALE processing
		Pointless processing
		shelx.hkl
		shelx.ins (optional)


## Helper tools

### make_shelx.py

Creates a shelx input file. Requires `sginfo` to be available on the system path to generate the SYMM/LATT cards.

	In:  cell, space group, composition
	Out: shelx.ins

Usage:

```
scedtools.make_shelx -c 10.0 20.0 30.0 90.0 90.0 90.0 -s Cmmm -m Si180 O360
```

### run_pointless.py

Looks for XDS_ASCII.HKL files specified in the cells.yaml, or on the command line and runs Pointless on them.

	In:  cells.yaml / XDS_ASCII.HKL
	Out: Pointless processing

### update.xds.py

Looks files matching `CORRECT.LP` in all subdirectories, and updates the cell parameters / space group as specified.

	In:  XDS.INP
	Out: XDS.INP

Usage:

```
scedtools.update_xds -c 10.0 20.0 30.0 90.0 90.0 90.0 -s Cmmm
```

## Requirements

- Python3.6 including `numpy`, `scipy`, `matplotlib`, and `pandas` libraries
- `sginfo` must be available on the system path
- (Windows 10) Access to [WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux)
- (Windows 10) XDS and related tools must be available under WSL

