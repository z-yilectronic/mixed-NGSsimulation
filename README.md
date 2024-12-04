# simulation

Files needed: 
```sh
reference chloroplast genome (Betula Nana)```
,
```sh
variations file e.g. vcf```

Using NGSNGS to generate fastq or mapping files, setting numbers of reads 3000~12000 for each round. Each round represents the reads from one individual, then we mix the reads of rounds together, to indicate there are 10 individuals in the simulating sample.

The core command:

```sh
./NGSNGS/ngsngs --input $input --reads $reads --lengthdist LogNorm,3.5,0.35 --lowerlimit 30 -circ -vcf Betula_old.q25.vcf.gz -id 0 -seq SE --qualityscore 30 --format fq --output $output
```
For some of the individuals, introduce random variations:
```sh
--variations 3```


Repeat

Mix generated reads for simulation
