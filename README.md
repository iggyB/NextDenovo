[![Downloads](https://img.shields.io/github/downloads/Nextomics/NextDenovo/total?logo=github)](https://github.com/Nextomics/NextDenovo/releases/download/v2.2-beta.0/NextDenovo.tgz)
[![Release](https://img.shields.io/github/release/Nextomics/NextDenovo.svg)](https://github.com/Nextomics/NextDenovo/releases)
[![Issues](https://img.shields.io/github/issues/Nextomics/NextDenovo.svg)](https://github.com/Nextomics/NextDenovo/issues)

# NextDenovo
NextDenovo is a string graph-based *de novo* assembler for TGS long reads. It uses a "correct-then-assemble" strategy similar to canu, but requires significantly less computing resources and storages. After assembly, the per-base error rate is about 97-98%, to further improve single base accuracy, please use [NextPolish](https://github.com/Nextomics/NextPolish).

NextDenovo contains two core modules: NextCorrect and NextGraph. NextCorrect can be used to correct TGS long reads with approximately 15% sequencing errors, and NextGraph can be used to construct a string graph with corrected reads. It also contains a modified version of [minimap2](https://github.com/lh3/minimap2) for adapting input and output and producing more sensitive and accurate dovetail overlaps, and some useful utilities (see [here](./doc/UTILITY.md) for more details).

So far, we have applied NextDenovo to dozens of species with various genome sizes ranging from megabytes’ level of bacteria (\~5 MB) or fungi (\~40 MB) to gigabytes’ level of mammals (\~3 GB) or gymnosperm plants (\~20 Gb). Especially, using Nanopore ultra-long reads, we have achieved genome assemblies of a maize with contig N50 of 66 Mb, a potato with contig N50 of 57 Mb, and a rice with contig N50 of 29 Mb, of which both the maize and potato contain large amounts of repetitive elements that hamper genome assembly with long contiguity and thus have a much more fragmented assembly versions in the public database compared to the one we achieved. In addition, the rice genome assembly contain merely 18 contigs in total, revealing complete assemblies for several chromosomes (Telomere to telomere with centromere in between). In addition, we found that NextDenovo, of the current version, might produce a small number of unexpected connection errors in the highly repetitive regions, which, however, can be easily corrected using additional Hi-C or Bionano data. We are still in a progress of optimizing NextDenovo and will continuously update it, especially in terms of assembly accuracy.

* **REQUIREMENT**
	* [Python 2.7](https://www.python.org/download/releases/2.7/)
	* [Psutil](https://psutil.readthedocs.io/en/latest/)
	* [Drmaa](https://github.com/pygridtools/drmaa-python) (Only required by running under non-local system)

* **DOWNLOAD**   
click [here](https://github.com/Nextomics/NextDenovo/releases/download/v2.2-beta.0/NextDenovo.tgz) or use the following command:   
`wget https://github.com/Nextomics/NextDenovo/releases/download/v2.2-beta.0/NextDenovo.tgz`  

* **INSTALL**  
~~`tar -vxzf NextDenovo.tgz && cd NextDenovo && make`~~

* **UNINSTALL**  
~~`cd NextDenovo && make clean`~~

* **TEST**  
`nextDenovo test_data/run.cfg`

* **QUICK RUN**  
	1. Prepare input.fofn  
    	`ls reads1.fasta reads2.fastq reads3.fasta.gz reads4.fastq.gz ... > input.fofn`
    2. Create run.cfg  
        `cp NextDenovo/doc/run.cfg ./`    
        ***Note:*** Please change the value of seed_cutoff using [bin/seq_stat](./doc/UTILITY.md#seq_stat) and refer to [doc/OPTION](doc/OPTION.md) to optimize parallel computing parameters. 
    3. Run  
        `nextDenovo run.cfg`
    4. Result
    	- Finally assembly genome with default parameters  
        `01_rundir/02.cns_align/01.get_cns.sh.work/get_cns000/nextgraph.assembly.contig.fasta` 
    	- Final assembly results  
    	`01_rundir/03.ctg_graph/01.ctg_graph.sh.work/ctg_graph*/*.e`

* **USAGE**    
Please see [doc/OPTION](doc/OPTION.md) for options introduction, see [doc/TEST](doc/TEST.md) for a tutorial about using NextDenovo to assemble the genome of HG002_NA24385_son.

* **HELP**   
Please raise an issue at the [issue page](https://github.com/Nextomics/NextDenovo/issues/new).

* **Contact information**    
For additional help, please send an email to huj_at_grandomics_dot_com.

* **COPYRIGHT**    
NextDenovo is freely available for academic use and other non-commercial use. For commercial use, please contact [NextOmics](https://www.nextomics.cn/en/).

* **PLEASE STAR AND THANKS** 

* **FAQ**  
	1. Which job scheduling systems are supported by NextDenovo?  
	NextDenovo uses [DRMAA](https://en.wikipedia.org/wiki/DRMAA) to submit, control, and monitor jobs, so theoretically it supports all DRMAA-compliant systems, such as LOCAL, SGE, PBS, SLURM. See [ParallelTask](https://github.com/moold/ParallelTask) to configure drmaa.
	2. How to continue running unfinished tasks?  
	No need to make any changes, simply run the same command again.
	3. How to reduce the total number of subtasks?  
	Please increase blocksize and reduce seed_cutfiles.
	4. How to speed up NextDenovo?  
	Currently, the bottlenecks of NextDenovo are minimap2 and IO. For minimap2, please see [here](https://github.com/lh3/minimap2/issues/322) to accelerate minimap2, besides, you can increase -l to reduce result size and disk consumption. For IO, you can check how many activated subtasks using top/htop, in theory, it should be equal to the -p parameter defined in correction_options. Use usetempdir will reduce IO wait, especially if usetempdir is on a SSD driver.
	5. How to specify the queue cpu/memory/bash to submit jobs?  
	Please use cluster_options, NextDenovo will replace {vf}, {cpu}, {bash} with specific values needed for each jobs.
	6. RuntimeError: Could not find drmaa library.  Please specify its full path using the environment variable DRMAA_LIBRARY_PATH.   
	Please setup the environment variable: DRMAA_LIBRARY_PATH, see [here](https://github.com/pygridtools/drmaa-python) for more details.
	7. ERROR: drmaa.errors.DeniedByDrmException: code 17: error: no suitable queues.  
	This is usually caused by a wrong setting of cluster_options, please check cluster_options first. If you use SGE, you also can add '-w n' to cluster_options, it will switch off validation for invalid resource requests. Please add a similar option for other job scheduling systems. 

