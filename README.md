# Simulation

Files needed: 
`reference chloroplast genome (Betula Nana)`, `variations file e.g. vcf`

Using NGSNGS (https://github.com/RAHenriksen/NGSNGS) to generate fastq or mapping files for simulation.

# A mock mix of reads from multiple individuals 

To test the possible method of inferring the number of individuals from a metagenome.

I set numbers of reads 5000~15000 for each round, which generates the reads from one individual. I use Log-normal distribution(mean=3.5, variance=0.35) as the length distribution, and a fixed sequencing quality 30 for all sites. 

To add the variations to each individual, I will randomly call 300 out of 585 variants (DP>1) in every simulating rounds.
```
bcftools view -i 'ALT!="."&&DP>1' Betula_old.q25.vcf.gz -o Betula_old_var_dp1.vcf
grep "^#" Betula_old_var_dp1.vcf > head.txt
grep -v "^#" Betula_old_var_dp1.vcf > vars.txt
for i in {1..5}; do cat head.txt <(shuf -n 300 vars.txt) > Betula_old_subvar_$i.vcf; done
```

Not knowing the "true" numbers of variants in an individual, I choose 300 variants to provide a more evident difference between different individuals.

The core command for generating reads:

```
for i in {1..5}; do
    output="${output_base}_${i}.fq"
    reads=$((2500 + i * 2500))
./NGSNGS/ngsngs
--input $input --reads $reads --lengthdist LogNorm,3.5,0.35 --lowerlimit 30
-circ -vcf Betula_old_subvar_$i.vcf -id 0 -seq SE --qualityscore 30
--format fq --output $output
```

With `ngssimulate_vcf.sh` running it 5 times, I get five sets of reads. Then I merged all the fastq files:
```
cat output_ngssim_*.fq.fq > ngssim_5ind.fq
```

A read file of simulated mixed individuals `ngssim_5ind.fq`.
