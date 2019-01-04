<html><body><p>
	I ran into an issue where I tried to use known SNPs from dbSNP that consisted of some empty alleles. If you are using GATK's base quality score recalibrator (BQSR), then it will complain about the VCF not being valid. There are a handful of Perl one-liners that are posted within forums, but they did not appear to work. Here is my solution.
</p>

<p>
	The script below takes a VCF as input (gzipped or not) and writes a new VCF. It simply checks for empty REF and ALT fields. When that criteria is met, these variant lines are skipped while writing out the new VCF.
</p>

```python
#! /usr/bin/env python

# This script removes empty alleles from a VCF. Some data sources such as
# dbSNP provide improperly formatted VCFs. This causes headache when trying
# to use strict tools such as the GATK.

# It works by checking for empty string in the REF and ALT columns. When
# one of these lines are crossed it skips writing it to the new VCF output.

import argparse
import gzip

def parse_args():
    """
    Parses the command line arguments.
    """
    parser = argparse.ArgumentParser(description='Strip empty alleles from VCF')
    parser.add_argument('input', type=str, help='Input VCF - gzip accepted')
    parser.add_argument('output', type=str, help='Output VCF')

    return parser.parse_args()

def vcf_open(path):
    """
    Obtain file handle if gzipped or normal text file.
    """
    if path.endswith('.gz'):
        return gzip.open(path, 'rt')

    return open(path, 'r')

def read_and_write_vcfs(input, output):
    """
    Reads in VCF and write out corrected VCF.
    """
    with vcf_open(input) as f:
        with open(output, 'w') as w:
            for line in f:
                if not line.startswith('#'):
                    records = line.strip().split("\t")
                    ref = records[3]
                    alt = records[4]

                    if is_empty(ref) or is_empty(alt):
                        print("skipping.... " + line)
                        continue

                w.write(line)

def main():
    args = parse_args()
    read_and_write_vcfs(args.input, args.output)

if __name__ == '__main__':
    main()
```
</body></html>