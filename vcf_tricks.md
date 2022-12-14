# VCF file related hacks

### 1. Finding the most common genotype

The `vcf` file encodes genotypes for each individual as either `1/1` , `1/0`, `0/1`, `0/0` and `./.` to denote the various genotype state.
A common task is to find if there are particular state more frequent in a file. You can do this with `awk` using:


```bash
awk 'BEGIN{
  OFS=FS="\t"} !/^#/ {
  print $1,$2,$3,"0/1:"gsub(/0\/1/,""),"1/0:"gsub(/1\/0/,""),"1/1:"gsub(/1\/1/,""),"0/0:"gsub(/0\/0/,""),"./.:"gsub(/\.\/\./,"")
}' yourfile.vcf |\
awk '{
  m=substr(m,5); for(i=4;i<=NF;i++)  if(substr(m,5) + 0  < substr($i,5) + 0) m=$i; print $0"\t"m
}'
```

which will generate the table with counts for each state and report the max state (last column)

```
1       16626   S1_16626        0/1:0   1/0:0   1/1:2   0/0:25  ./.:0   0/0:25
1       17673   S1_17673        0/1:1   1/0:0   1/1:1   0/0:25  ./.:0   0/0:25
1       18684   S1_18684        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       19738   S1_19738        0/1:0   1/0:0   1/1:2   0/0:25  ./.:0   0/0:25
1       20740   S1_20740        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       21756   S1_21756        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       22804   S1_22804        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       23925   S1_23925        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       25044   S1_25044        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       26051   S1_26051        0/1:3   1/0:0   1/1:0   0/0:24  ./.:0   0/0:24
1       27084   S1_27084        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       28130   S1_28130        0/1:0   1/0:0   1/1:2   0/0:25  ./.:0   0/0:25
1       29173   S1_29173        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       30176   S1_30176        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       31268   S1_31268        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       32295   S1_32295        0/1:1   1/0:0   1/1:2   0/0:24  ./.:0   0/0:24
1       33442   S1_33442        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       34444   S1_34444        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       35457   S1_35457        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       38225   S1_38225        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       39269   S1_39269        0/1:5   1/0:0   1/1:0   0/0:22  ./.:0   0/0:22
1       40298   S1_40298        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       41392   S1_41392        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       42711   S1_42711        0/1:0   1/0:0   1/1:1   0/0:26  ./.:0   0/0:26
1       44049   S1_44049        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       45125   S1_45125        0/1:1   1/0:0   1/1:0   0/0:26  ./.:0   0/0:26
1       46446   S1_46446        0/1:0   1/0:0   1/1:2   0/0:25  ./.:0   0/0:25
1       47453   S1_47453        0/1:2   1/0:0   1/1:0   0/0:25  ./.:0   0/0:25
```
Note that add `0` is needed here to convert the string to numeric for comparison purpose.


### 2. Print bi-allelic SNPs only

Bi-allelic SNPs as the name suggests, have only two allelic variants segregating in the population. Sometimes it is useful to filter your `vcf` file to just retain them. To do so:  

```bash
awk 'BEGIN{OFS=FS="\t"} !/^#/ (($4!~"," && $5!~"," && length($4)==1 && length($5)==1))' yourfile.vcf > biAllelic.vcf
```
