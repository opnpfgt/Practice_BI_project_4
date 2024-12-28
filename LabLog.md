# Practice_BI_project_4

1. Obtain the data

```bash
wget http://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/001/949/185/GCA_001949185.1_Rvar_4.0/GCA_001949185.1_Rvar_4.0_genomic.fna.gz
gunzip -c GCA_001949185.1_Rvar_4.0_genomic.fna.gz > my_tardig.fna
```

2. Get AUGUSTUS results and count number of proteins

2.1. Download augustus.whole.gff manually

2.2. Download getAnnoFasta.pl script and ,ake it executable
```bash
 wget http://augustus.gobics.de/binaries/scripts/getAnnoFasta.pl
 chmod u+x getAnnoFasta.pl
```
2.3.
 Run the script and count prots

```bash
 perl getAnnoFasta.pl augustus.gff
 grep ">" augustus.whole.aa | wc -l
```

Output
```bash
16435
```

Consistent with the expected results

3. Physical localization 

```bash
 makeblastdb -in augustus.whole.aa -dbtype prot  -out augtarDB
 blastp -db augtarDB -query peptides.fa -outfmt 6  -out matched_prots.tsv
 cat matched_prots.tsv | awk '{FS="\t";OFS="\t"} {print $2}' | sort | uniq > genames.txt
 xargs samtools faidx augustus.whole.aa < genames.txt > interesting_prots.fa
```

34 unique prots


Second way to extract proteins of interest from the initial file:

diamond makedb --in augustus.whole.aa   --db diamond_db
diamond blastp -d diamond_db.dmnd -q peptides.fa   -f 6 -o diamond_output --very-sensitive

1  |           |     |    |   |   |   |    |     |     |          |      | 
---|-----------|-----|----|---|---|---|----|-----|-----|----------|------|
8  | g4106.t1  | 100 | 18 | 0 | 0 | 1 | 18 | 222 | 239 | 5.55e-07 | 40.8 |
20 | g12510.t1 | 100 | 18 | 0 | 0 | 1 | 18 | 425 | 442 | 1.43e-06 | 39.7 |
21 | g12510.t1 | 100 | 22 | 0 | 0 | 1 | 22 | 443 | 464 | 2.46e-09 | 47.8 |
29 | g4106.t1  | 100 | 18 | 0 | 0 | 1 | 18 | 222 | 239 | 5.55e-07 | 40.8 |



4. Localization prediction

a) Load "interesting_prots.fa" in Wolf with status animal

Result:

https://wolfpsort.hgc.jp/results/aKC6ea7340898acff7777ae26d1c8888f7a.html

b) Uniq_prots.fasta >>> TargetP-2.0 >>>  output_protein_type.txt

grep 'nucl'  wolf_output.txt | awk '{print $1}' > wolf_nucl.text

comm -12 wolf_nucl.text target_OTHER.txt > intersect_wolf_target.txt
wc -l intersect_wolf_target.txt
     16 intersect_wolf_target.txt

