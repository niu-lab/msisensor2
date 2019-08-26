MSIsensor2
===========
MSIsensor2 is a novel algorithm based machine learning, featuring a large upgrade in the microsatellite instability (MSI) detection for tumor only or Cell-Free Tumor DNA (ctDNA) sequencing data. The original MSIsensor is specially designed for tumor/normal paired sequencing data.

Result
-------

Given tumor only sequencing data, MSIsensor2 uses machine learning models to figure out the MSI status for a distribution per microsatellite. Finally, the msi score (number of msi sites / all valid sites) can be calculated. In our test of 117 EGA samples, results from tumor only module are comparable with paired tumor and normal sequencing data input (see two result figures below) . The recommended msi score cutoff value is 20% (msi high: msi score >= 20%). We also tested TCGA and EGA data whose results showed the accuracy of tumor only module is up to 99% and illustrated the comparable performance advantage of tumor only module over original MSIsensor tumor/normal paired module (see ROC figures below) . In addition, for the tumor only module, we also tested ctDNA sequencing data from different companies. The results showed that MSIsensor2 can accurately discriminate the microsatellite status of ctDNA sequencing samples.

![](https://github.com/niu-lab/msisensor2/blob/master/test/fig2.pairresult.png)
![](https://github.com/niu-lab/msisensor2/blob/master/test/fig3tumoronlyresultproduct.png)
![](https://github.com/niu-lab/msisensor2/blob/master/test/fig4.rocplot.png)


Tumor Only vs Paired
-------

Correlation coefficient between the two modules is 0.94, that levels of the msi score of different samples perform similarly under the two modules.High R-square shows that even though the two modules use different algorithms, their results are highly consistent, which fully demonstrates that the result of our tumor only module is reliable.

![](https://github.com/niu-lab/msisensor2/blob/master/test/fig5.cor.png)

MSIsensor2 vs TSO500
-------

To further illustrate the accuracy of tumor only module in MSIsensor2, we deliberately customized a model for the TSO500-panel and tested it with 10 samples. The results are shown in the table on the right-hand side. It is not difficult to see that the model we customized for the TSO500 is consistent with its own ability to discriminate the microsatellite status of the sample. In addition, MSIsensor2 is not critical to sequencing methods or sequencing types, which refers that MSIsensor2 can handle both target gene sequencing data and amplification sequencing data, furthermore, it is applicable for WES, WGS, panel and ctDNA data.

![](https://github.com/niu-lab/msisensor2/blob/master/test/table.png)

Faster!
-------

By testing 117 samples from EGA with different types of sequencing data, in average, the tumor only module is 10 times faster than the paired module of MSIsensor. A typical WES data can be finished within 180 seconds.

![](https://github.com/ding-lab/msisensor/blob/master/test/fig6.timeplot.png)

Install
-------

Currently, MSIsensor2 is based on Linux system, and we provide binaries only. Please note your GCC version should be at least 4.8.x.

        git clone https://github.com/niu-lab/msisensor.git
        cd msisensor2
        chmod +x msisensor2

Usage
-----

        Version 0.1
        Usage:  msisensor2 <command> [options]

Key commands:

        scan            scan homopolymers and miscrosatelites
        msi             msi scoring

msisensor2 scan [options]:

       -d   <string>   reference genome sequences file, *.fasta format
       -o   <string>   output homopolymer and microsatelittes file

       -l   <int>      minimal homopolymer size, default=5
       -c   <int>      context length, default=5
       -m   <int>      maximal homopolymer size, default=50
       -s   <int>      maximal length of microsate, default=5
       -r   <int>      minimal repeat times of microsate, default=3
       -p   <int>      output homopolymer only, 0: no; 1: yes, default=0

       -h   help

msisensor2 msi [options]:

       -d   <string>   homopolymer and microsates file
       -n   <string>   normal bam file
       -t   <string>   tumor  bam file
       -M   <string>   models directory for tumor only data
       -o   <string>   output distribution file

       -e   <string>   bed file, optional
       -f   <double>   FDR threshold for somatic sites detection, default=0.05
       -c   <int>      coverage threshold for msi analysis, WXS: 20; WGS: 15, default=20
       -z   <int>      coverage normalization for paired tumor and normal data, 0: no; 1: yes, default=0
       -r   <string>   choose one region, format: 1:10000000-20000000
       -l   <int>      minimal homopolymer size, default=5
       -p   <int>      minimal homopolymer size for distribution analysis, default=10
       -m   <int>      maximal homopolymer size for distribution analysis, default=50
       -q   <int>      minimal microsates size, default=3
       -s   <int>      minimal microsates size for distribution analysis, default=5
       -w   <int>      maximal microstaes size for distribution analysis, default=40
       -u   <int>      span size around window for extracting reads, default=500
       -b   <int>      threads number for parallel computing, default=1
       -x   <int>      output homopolymer only, 0: no; 1: yes, default=0
       -y   <int>      output microsatellite only, 0: no; 1: yes, default=0

       -h   help

Example
-------

1. MSI scoring:

   for tumor only sequence data:

        msisensor2 msi -M ./models_hg38 -t tumor.bam -o output.tumor.prefix

   Note: normal and tumor bam index files are needed in the same directory as bam files

Output
-------
The list of microsatellites is output in "scan" step. The MSI scoring step produces 4 files:

        output.prefix
        output.prefix_dis_tab
        output.prefix_germline
        output.prefix_somatic

for tumor only input, the MSI scoreing step produces 3 files: 

        output.tumor.prefix
        output.tumor.prefix_dis_tab
        output.tumor.prefix_somatic

1. microsatellites.list: microsatellite list output ( columns with *_binary means: binary conversion of DNA bases based on A=00, C=01, G=10, and T=11 )

        chromosome      location        repeat_unit_length     repeat_unit_binary    repeat_times    left_flank_binary     right_flank_binary      repeat_unit_bases      left_flank_bases       right_flank_bases
        1       10485   4       149     3       150     685     GCCC    AGCCG   GGGTC
        1       10629   2       9       3       258     409     GC      CAAAG   CGCGC
        1       10652   2       2       3       665     614     AG      GGCGC   GCGCG
        1       10658   2       9       3       546     409     GC      GAGAG   CGCGC
        1       10681   2       2       3       665     614     AG      GGCGC   GCGCG

2. output.prefix: msi score output

        Total_Number_of_Sites   Number_of_Somatic_Sites %
        640     75      11.72

3. output.prefix_dis_tab: read count distribution (N: normal; T: tumor)

        1       16248728        ACCTC   11      T       AAAGG   N       0       0       0       0       1       38      0       0       0       0       0       0       0
        1       16248728        ACCTC   11      T       AAAGG   T       0       0       0       0       17      22      1       0       0       0       0       0       0

4. output.prefix_somatic: somatic sites detected ( FDR: false discovery rate )

        chromosome   location        left_flank     repeat_times    repeat_unit_bases    right_flank      difference      P_value    FDR     rank
        1       16200729        TAAGA   10      T       CTTGT   0.55652 2.8973e-15      1.8542e-12      1
        1       75614380        TTTAC   14      T       AAGGT   0.82764 5.1515e-15      1.6485e-12      2
        1       70654981        CCAGG   21      A       GATGA   0.80556 1e-14   2.1333e-12      3
        1       65138787        GTTTG   13      A       CAGCT   0.8653  1e-14   1.6e-12 4
        1       35885046        TTCTC   11      T       CCCCT   0.84682 1e-14   1.28e-12        5
        1       75172756        GTGGT   14      A       GAAAA   0.57471 1e-14   1.0667e-12      6
        1       76257074        TGGAA   14      T       GAGTC   0.66023 1e-14   9.1429e-13      7
        1       33087567        TAGAG   16      A       GGAAA   0.53141 1e-14   8e-13   8
        1       41456808        CTAAC   14      T       CTTTT   0.76286 1e-14   7.1111e-13      9

5. output.prefix_germline: germline sites detected

        chromosome   location        left_flank     repeat_times    repeat_unit_bases    right_flank      genotype
        1       1192105 AATAC   11      A       TTAGC   5|5
        1       1330899 CTGCC   5       AG      CACAG   5|5
        1       1598690 AATAC   12      A       TTAGC   5|5
        1       1605407 AAAAG   14      A       GAAAA   1|1
        1       2118724 TTTTC   11      T       CTTTT   1|1


Test sample
-------
We provided one small dataset (tumor and matched normal bam files) to test the msi scoring step:

        cd ./test
        bash run.sh

We also provided a R script to visualize MSI score distribution of MSIsensor2 output. ( msi score list only or msi score list accompanied with known msi status). For msi score list only as input: 

        R CMD BATCH "--args msi_score_only_list msi_score_only_distribution.pdf" plot.r
![](https://github.com/ding-lab/msisensor/blob/master/test/msi_score_only_distribution.jpg)

For msi score list accompanied with known msi status as input:

        R CMD BATCH "--args msi_score_and_status_list msi_score_and_status_distribution.pdf" plot.r
![](https://github.com/ding-lab/msisensor/blob/master/test/msi_score_and_status_distribution.jpg)


Contact
-------
If you have any questions, please contact one or more of the following folks:
Beifang Niu <bniu@sccas.cn>
Kai Ye <kaiye@xjtu.edu.cn>
