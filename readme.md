# ACCURATE DETECTION OF CONVERGENT MUTATIONS IN LARGE PROTEIN ALIGNMENTS WITH CONDOR

### Marie MOREL, Frédéric LEMOINE, Anna ZHUKOVA and Olivier GASCUEL

Welcome to ConDor workflow repository ! 

In this repository you can find our ConDor workflow developed to detect convergent evolution in amino acid alignments. 
ConDor is available from a web service located at https://condor.pasteur.cloud/.

ConDor is adapted to the detection of evolutionary convergence at the resolution of a mutation especially in large datasets (several hundreds of sequences). To run ConDor you will need a multiple sequence alignment in fasta format including outgroup sequences, the corresponding tree in newick (with same sequence names as in the alignment), a file with outgroup sequence names and a file containing a list of sequences with the convergent phenotype (can be optional).

ConDor is composed of two independent components Emergence and Correlation, which can be launched together or not.

The output of ConDor is a list of mutations detected as convergent as they occur more often than expected under neutral evolution (Emergence component) and/or correlate (Correlation component) with the convergent phenotype (or a predictor of the phenotype). 


# Usage
To launch a full analysis (condor = both Emergence and Correlation components) with the test data (sedge dataset) run : 

    nextflow run condor.nf --align test_data/cyp_coding.aa.coor_mays.fa --tree test_data/cyp_coding.phy_phyml_tree.txt --outgroup test_data/outgroup.txt --phenotype besnard2009_convergent_species.txt --resdir output --model best --nb_simu 100 --min_seq 2 --min_eem 2 --freqmode Fmodel --branches condor --correction holm --alpha 0.1 --bayes 2

You can also chose to run only the Emergence component. In this case you would not need to provide the phenotype file nor BayesTraits parameters : 

    nextflow run condor.nf --align test_data/cyp_coding.aa.coor_mays.fa --tree test_data/cyp_coding.phy_phyml_tree.txt --outgroup test_data/outgroup.txt --resdir output --model best --nb_simu 100 --min_seq 2 --min_eem 2 --freqmode Fmodel --branches emergence --correction holm --alpha 0.1

Finally, if you want to run only the Correlation component: 

    nextflow run condor.nf --align test_data/cyp_coding.aa.coor_mays.fa --tree test_data/cyp_coding.phy_phyml_tree.txt --outgroup test_data/outgroup.txt --phenotype besnard2009_convergent_species.txt --resdir output --model best --min_seq 2 --min_eem 2 --freqmode Fmodel --branches correlation --bayes 2

For larger datasets (>1000), we recommand to increase --min_seq 10 and --bayes 20

# Outputs

Two output files (csv) are given by ConDor:

1. Tested_results.tsv: all mutations tested by ConDor with multiple metrics and statistics. The columns are described below.  
2. Significant_results.tsv: only mutations which p-value and log Bayes Factor passed the acceptance threshold and are thus considered as convergent.  

Example results can be found <a href="https://condor.pasteur.cloud/view/7c9b6049-96aa-471a-47de-a184817db0b2">here.</a>  

For this example, we used the dataset from <a href=https://doi.org/10.1093/molbev/msp103>(Besnard *et al.*, 2009) </a> used in the PCOC paper <a href=https://academic.oup.com/mbe/article-pdf/35/9/2296/25534515/msy114.pdf>(Rey *et al.* 2018)</a>.
It consists of 79 sequences of the PEPC protein in sedges (plant species at C3/C4 transition) and the corresponding tree. 

**Metrics**

- **pastml_root**: Ancestral amino acid reconstructed at this position by [PastML](https://academic.oup.com/mbe/article/36/9/2069/5498561?login=true).
- **consensus_root**: Amino acid that is most frequent at this position. 
- **position**: Position in the alignment. 
- **mut**: Amino acid tested for convergence at this position. 
- **max_anc**: Amino acid from which EEMs are most often issued. 
- **ref_EEM**: Number of EEMs for the tested amino acid.
- **nbseq**: Number of sequences exhibiting this amino acid at this position. 
- **evol_rate**: Rate of evolution of the position.
- **genetic_distance**: Minimal number of DNA substitutions in the codon to switch between the two amino acids. 
- **substitution rate**: Value that indicates how exchangeable two amino acids are. If they can switch very easily (high substitution rate), we expect a lot of EEMs in the simulations, and then, the mutation is difficult to detect even if it is truly convergent. The substitution rate is given by the matrix of the substitution model (e.g. HIVb and MtZoa in the paper).
- **findability**: Inverse of the substitution rate. 
- **type_substitution**: Category of the mutation: convergent (issued from several ancestral amino acids), parallel (always issued from the same ancestral amino acid) or revertant (go back to the root amino acid). 
- **details**: Ancestral amino acid(s) for the EEMs and how many EEMs are issued from it (them).
- **loss**: Number of times this newly acquired amino acid is lost (It becomes the ancestral amino acid in an other EEM).
- **loss_details**: Towards which amino acids can we observe a loss.
- **max_simu**: Maximum number of EEMs in the simulations.
- **variance**: Variance of the number of EEMs in the simulations.
- **mean**: Mean of the number of EEMs in the simulations.
- **pvalue_raw**: p-value corresponding to the number of simulations with more EEMs than observed (ref-emerge) divided by the number of simulations.
- **adjust_pvalue**:
- **adjust_pvalue_fdr**:
- **detected_EEM**: If the mutation passed the acceptance threshold or not for the Emergence component.
- **posmut**: joint position and amino acid tested for convergence at this position. 
- **log-dep**: log likelihood of BayesTraits for the dependence model
- **log-indep**: log likelihood of BayesTraits for the independence model
- **BF**: log Bayes Factor
- **correlation**: positive or negative according to phenotype

# Prerequisite 
https://www.nextflow.io/docs/latest/getstarted.html#requirements

# Help
Please visit https://condor.pasteur.cloud/help for more details regarding how to use ConDor and interpret the outputs. 



