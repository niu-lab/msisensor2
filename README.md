MSIsensor2
===========
MSIsensor2 is a novel algorithm based machine learning, featuring a large upgrade in the microsatellite instability (MSI) detection for tumor only sequencing data, including Cell-Free DNA (cfDNA), Formalin-Fixed Paraffin-Embedded(FFPE) and other sample types. The original MSIsensor is specially designed for tumor/normal paired sequencing data.

Result
-------

Given tumor only sequencing data, MSIsensor2 uses machine learning models to figure out the MSI status for a distribution per microsatellite. Finally, the msi score (number of msi sites / all valid sites) can be calculated. In our test of 117 EGA samples, results from tumor only module are comparable with paired tumor and normal sequencing data input (see two result figures below) . The recommended msi score cutoff value is 20% (msi high: msi score >= 20%). We also tested TCGA and EGA data whose results showed the accuracy of tumor only module is up to 99% and illustrated the comparable performance advantage of tumor only module over original MSIsensor tumor/normal paired module (see ROC figures below) . In addition, for the tumor only module, we also tested cfDNA sequencing data from different companies. The results showed that MSIsensor2 can accurately discriminate the microsatellite status of cfDNA (>0.1% tumor content) sequencing samples.

![](https://github.com/niu-lab/msisensor2/blob/master/test/fig2.pairresult.png)
![](https://github.com/niu-lab/msisensor2/blob/master/test/fig3tumoronlyresultproduct.png)
![](https://github.com/niu-lab/msisensor2/blob/master/test/fig4.rocplot.png)


Tumor Only vs Paired
-------

Correlation coefficient between the two modules is 0.94, that levels of the msi score of different samples perform similarly under the two modules.High R-square shows that even though the two modules use different algorithms, their results are highly consistent, which fully demonstrates that the result of our tumor only module is reliable.

![](https://github.com/niu-lab/msisensor2/blob/master/test/fig5.cor.png)

MSIsensor2 vs TSO500
-------

To further illustrate the accuracy of tumor only module in MSIsensor2, we deliberately customized a model for the TSO500-panel and tested it with 10 samples. The results are shown in the table below. It is not difficult to see that the model we customized for the TSO500 is consistent with its own ability to discriminate the microsatellite status of the sample. In addition, MSIsensor2 is not critical to sequencing methods or sequencing types, which refers that MSIsensor2 can handle both target gene sequencing data and amplification sequencing data, furthermore, it is applicable for WES, WGS, panel and cfDNA data.

![](https://github.com/niu-lab/msisensor2/blob/master/test/table.png)

Faster!
-------

By testing 117 samples from EGA with different types of sequencing data, in average, the tumor only module is 10 times faster than the paired module of MSIsensor. A typical WES data can be finished within 180 seconds.

![](https://github.com/niu-lab/msisensor2/blob/master/test/fig6.timeplot.png)

Install
-------

Currently, MSIsensor2 is based on Linux system, and we provide binaries only. Please note your GCC version should be at least 4.8.x.

        git clone https://github.com/niu-lab/msisensor2.git
        cd msisensor2
        chmod +x msisensor2

Usage
-----

        Version 0.1
        Usage:  msisensor2 <command> [options]

Key commands:

        msi             msi scoring

msisensor2 msi [options]:

       -M   <string>   models directory for tumor only data
       -t   <string>   tumor  bam file
       -o   <string>   output distribution file

       -c   <int>      coverage threshold for msi analysis, WXS: 20; WGS: 15, default=20
       -b   <int>      threads number for parallel computing, default=1
       -x   <int>      output homopolymer only, 0: no; 1: yes, default=0
       -y   <int>      output microsatellite only, 0: no; 1: yes, default=0

       -h   help

Example
-------

MSI scoring:

   for tumor only sequence data:

   hg38 bam:

        msisensor2 msi -M ./models_hg38 -t ./test/example.tumor.only.hg38.bam -o output.tumor.prefix

   hg19 or GRCh37 bam:
   
        msisensor2 msi -M ./models_hg19_GRCh37 -t ./test/example.tumor.only.hg19.bam -o output.tumor.prefix

   b37 or humanG1Kv37 bam:
   
        msisensor2 msi -M ./models_b37_HumanG1Kv37 -t ./test/example.tumor.only.b37.bam -o output.tumor.prefix

   Note: bam index files are needed in the same directory as bam files

Output
-------

for tumor only input, the MSI scoreing step produces 3 files: 

        output.tumor.prefix
        output.tumor.prefix_dis
        output.tumor.prefix_somatic

1. output.prefix: msi score output

        Total_Number_of_Sites   Number_of_Somatic_Sites %
        2     1      50.00

3. output.prefix_dis: read count distribution (T: tumor)

        chr22 29286892 AAAGC 12[T] CTCTT
        T: 0 0 0 0 0 0 0 0 25 71 4 86 3 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 

4. output.prefix_somatic: somatic sites detected

        chromosome   location        left_flank     repeat_times    repeat_unit_bases    right_flank    discrimination_value_ML

        chr22	29286892	AAAGC	12	T	CTCTT	0.98852

Test sample
-------
We provided one small dataset (tumor only bam file) to test the msi scoring step:

        msisensor2 msi -M ./models_hg38 -t ./test/example.tumor.only.hg38.bam -o output.tumor.prefix
        msisensor2 msi -M ./models_hg19 -t ./test/example.tumor.only.hg19.bam -o output.tumor.prefix

We also provided a R script to visualize MSI score distribution of MSIsensor2 output. ( msi score list only or msi score list accompanied with known msi status). For msi score list only as input: 

        R CMD BATCH "--args msi_score_only_list msi_score_only_distribution.pdf" plot.r
![](https://github.com/ding-lab/msisensor/blob/master/test/msi_score_only_distribution.jpg)

For msi score list accompanied with known msi status as input:

        R CMD BATCH "--args msi_score_and_status_list msi_score_and_status_distribution.pdf" plot.r
![](https://github.com/ding-lab/msisensor/blob/master/test/msi_score_and_status_distribution.jpg)


Contact
-------
If you have any questions, please contact one or more of the following folks:
Beifang Niu <beifang.cn@gmail.com>
