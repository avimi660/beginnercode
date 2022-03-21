# how to get into folders
17:31:09 mahuika01 ~ $ # log into nesi using password BlazeDogBest12
17:31:29 mahuika01 ~ $ #use second factor authentification 
17:31:42 mahuika01 ~ $ #use password BlazeDogBest12 again after 2nd factor 
17:32:00 mahuika01 ~ $ #check were i am
17:32:11 mahuika01 ~ $ pwd
/home/avimi660
17:32:17 mahuika01 ~ $ # ls command
17:32:45 mahuika01 ~ $ ls
michael  RNAseq 12.sh  RNASeq script.  RNASeq script.sh  scripting_workshop
17:32:46 mahuika01 ~ $ # open michael directory
17:33:01 mahuika01 ~ $ cd michael
17:33:10 mahuika01 ~/michael $ # see whats in the directory 
17:33:32 mahuika01 ~/michael $ ls
mitoproteome_master  possum_genome_master
17:33:47 mahuika01 ~/michael $ # to open either folder 
17:34:04 mahuika01 ~/michael $ cd "file name"


# head -n 2 = read read the first two lines of the file - mito_genes. cut
search_term=`head -n 2 mito_genes.txt | tail -n 1 | cut -f 4`

 **
#grep command is used to search files for a string of characters in a specified file - so in this line below we are searching for the term ABAT within the direcotry in the line below in the GCF file. 
grep ABAT /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t'

 
# searching the GCF file for the word "gene" as specified by the command grep $'\t'gene$'\t'
grep $search_term /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t'

 
# same command as before however this time we are directing the output of the command to a file called 'temp'
grep $search_term /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' > temp


 
# wc -l word count and number of lines awk print 1 means print the first column in the line 
no_matches=`wc -l temp | awk '{ print $1 }'`

 
# echo command means i am asking to display line of text for search_term and no_matches, the >> means I am asking it to add it to the existing results_matches.txt file
echo $search_term $no_matches >> results_matches.txt

# gene_search_term is assigned to the following code head -n 2 (first two lines of txt.) | also adding on the tail 1 line and cut -f 
gene_search_term=`head -n 2 mito_genes.txt | tail -n 1 | cut -f 4`

 
# searching 'gene' in the GCF file and redirecting output to temp file. > command will overwrite what is already existing in the file so be careful. 
grep $gene_search_term /nesi/nobackup/uoo03398/alana_demo/data/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' > temp

# alt_search_terms is assigned to the following code 
alt_search_terms=`head -n 2 mito_genes.txt | tail -n 1 | cut -f 5`
# number of alternate gene names displayed 
no_alts=`echo $alt_search_terms | grep -o ";" | wc -l`

# creating the loop
for alt_gene in `seq 1 1 $no_alts`; do echo $alt_gene; done

for alt_gene in `seq 1 1 $no_alts`; do echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'; done

for alt_gene in `seq 1 1 $no_alts`; do alt_gene_name=`echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'`; done

for alt_gene in `seq 1 1 $no_alts`; do alt_gene_name=`echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'`; echo $alt_gene_name; done

for alt_gene in `seq 1 1 $no_alts`; do alt_gene_name=`echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'`; echo $alt_gene_name; grep $alt_gene_name /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t'; done

for alt_gene in `seq 1 1 $no_alts`; do alt_gene_name=`echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'`; grep $alt_gene_name /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' >> temp; done
# cat = print the content of a file onto the standard output stream sort is used to arrange the order uniq is used to filter out repeated lines ie so we dont get the same line printing out for the same line in the file with multiple 
cat temp | sort | uniq > temp_all_searches

no_matches=`wc -l temp_all_searches | awk '{ print $1 }'`

echo $gene_search_term $no_matches >> results_matches.txt
 
cat temp_all_searches > gene_location_possum.txt
