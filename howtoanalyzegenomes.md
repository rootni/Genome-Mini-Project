# How to analyze genomes for genomes mini project

## Downloading your sequence
### From NCBI: 
1. Begin by downloading the NCBI command line tools and make them executable. <br> <br>
curl -o datasets https://ftp.ncbi.nlm.nih.gov/pub/datasets/command-line/v2/linux-amd64/datasets <br>
curl -o dataformat https://ftp.ncbi.nlm.nih.gov/pub/datasets/command-line/v2/linux-amd64/dataformat <br>
chmod +x datasets dataformat 

2. Now download the genome using its accession number. for ambystoma: <br> <br>	
./datasets download accession GCA_002915635.3

### From Axolotl-omics.org:
wget https://www.axolotl-omics.org/dl/AmexG_v6.0-DD.fa.gz

## How to isolate one chromosome from a sequence. 
Because of the size of the Ambystoma genome, the commands we used to analyze sequences could not be used on the entire genome by our computers. Instead, we ran a job on the discovery cluster to determine what individual sequences comprised our whole genome file. Alternatively, one could run a job on the discovery cluster which uses these same commands on the entire genome, however we still encountered memory allocation issues when attempting this. <br> <br>

1. You will first need to create a bash script which reads your whole genome and searches for all instances of '>' or the new sequence in a fasta file. This must be saved in a file with the extension .bash. <br> <br>
#!/bin/bash <br>
#SBATCH --partition=short <br>
#SBATCH --job-name=ambystomainfo <br>
#SBATCH --time=6:00:00 <br>
#SBATCH --nodes=1 <br>
#SBATCH --cpus-per-task=2 <br>
#SBATCH --mem=264G <br>
#SBATCH --output=%j.output <br>
#SBATCH --error=%j.error <br> <br>
cd /scratch/breen.d/ <br>
grep '>' AmexG_v6.0-DD.fa <br>
<br>

2. You must then run this bash script on the discovery cluster with the following command. Once the job has been completed, you can view your results under the file name <JOBID>.output <br> <br>
sbatch '<script name>'.bash <br> <br>

3. Once you have a list of sequences from your entire genome file, pick which one you would like to analyze. I chose the p arm of chromosome 14 of the ambystoma genome with the sequence identifier >chr14p. <br>
Create a new file containing only your desired sequence using the following commands: <br> <br>
echo 'chr14p' > seqname.txt <br>
seqtk subseq AmexG_v6.0-DD.fa seqname.txt > chromosome14.txt <br> <br>
The file chromosome14.txt now contains the sequence for only chromosome 14. <br> <br>
>chr14p
GGTTTTCTCTGTTTGCTTTTCCCTTCTCCTTGGGATAAGTTCTTTCTACTTACCTGACGGCCGTTTCAGTTTCTGCGGTGGCCTTCTTGTCT

## Generating the reverse complement of a sequence.
1. Once you have isolated the .fa or .fasta file for your sequence, load the relevant modules (emboss and seqtk) into your working directory. <br> <br>
module load emboss/6.6.0 <br>
module load seqtk

2. Generate the reverse compliment of a sequence into a new file reversecompliment.txt <br> <br>
revseq chromosome14.txt  reversecompliment.txt <br> <br>
>chr14p Reversed:
nnnnnnnnnnnnnnnnnnnnAATCCCACACAACCAGCTACACCACCTGAGAACTCCATGG


## Predicting the amino acid sequence of a sequence.
1. Once again, determine the name of the file you wish to translate and load the relevant modules (emboss and seqtk) <br> <br>
module load emboss/6.6.0 <br>
module load seqtk

2. Generate the predicted amino acid translation of a sequence into a new file AAsequence.txt <br> <br>
transeq chromosome14.txt  AAsequence.txt <br> <br>
>chr14p_1 <br>
GFLCLLFPSPWDKFFLLT*RPFQFLRWPSCLQPLPCRSLGTTSLFPL*SRRFGGRHVFSL <br>
LFAALFSRGSPVFPCFLCPLFPLP*FSLSYRAW*VLGPEVSSAPFTSHWCRFGLLSCVLP <br>


## Determining the GC content of a sequence.
1. Determine the name of the file you wish to determine the GC content of and load emboss and seqtk. <br> <br>
module load emboss/6.6.0 <br>
module load seqtk

2. Using the command infoseq displays basic information about sequences, inlcuding GC content. <br> <br>
infoseq chromosome14.txt  <br> <br>
Display basic information about sequences
USA                      Database  Name           Accession      Type Length %GC    Organism            Description
fasta::chromosome14.txt:chr14p -              chr14p         -              N    184706515 46.67
<br>
