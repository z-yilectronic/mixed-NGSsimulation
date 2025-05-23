# Simulation of aeDNA reads using NGSNGS

Files needed: 
`reference genome`, `variations file e.g. vcf`

We use NGSNGS (https://github.com/RAHenriksen/NGSNGS) to generate fastq or mapping files for simulation.

**The core command for generating reads:**

```
./NGSNGS/ngsngs
--input $input --reads $readsnumbers --lengthdist LogNorm,3.5,0.35 --lowerlimit 30
-circ -vcf Betula_old_subvar_$i.vcf -id 0 -seq SE --qualityscore 30
--format fq --output $output
```

**A mock mix of multiple individuals**

To test the possible methods of inferring the number of individuals from a metagenome.

Set numbers of reads 5000~15000 for each round, which generates the reads from one individual. I use Log-normal distribution(mean=3.5, variance=0.35) as the length distribution, and a fixed sequencing quality 30 for all sites. 

I randomly call 300 out of 585 variants (DP>1) in every simulating rounds to add the variations to each individual,
```
bcftools view -i 'ALT!="."&&DP>1' Betula_old.q25.vcf.gz -o var_dp1.vcf
grep "^#" var_dp1.vcf > head.txt
grep -v "^#" var_dp1.vcf > vars.txt
for i in {1..5}; do cat head.txt <(shuf -n 300 vars.txt) > subvar_$i.vcf; done
```

Not knowing the "true" number of variants in an individual, I choose 300 to provide a more evident difference between individuals.

Then merge all the fastq files:
```
cat output_ngssim_*.fq.fq > ngssim_5ind.fq
```

The file of simulated mixed reads `ngssim_5ind.fq`.
