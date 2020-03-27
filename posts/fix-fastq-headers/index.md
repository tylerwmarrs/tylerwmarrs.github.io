<html><body><p>
	I ran into a case in the lab where I received some odd FASTq headers. They were not standard by any means. The data consisted of paired end reads, but the headers did not contain the appropriate format for specifying which end it came from. Here is a script that I wrote to fix the issue. This may be useful to you as well, but be wary that I hard coded a hardware identifier of the sequencer within the header identification logic. This script also assumes that "read1" and "read2" is within the file name. It uses the filename to determine which end the FASTA came from.
</p>

```python
#! /usr/bin/env python

# Some of the FASTQ files have some odd format. So this script fixes the header
# to where it matches paired reads properly. BWA complains that they are 
# different.
#
# The read counts are also the same in each FASTQ.
# -------
# Example
# -------
# Read 1
# @D3LH75P1:3:1101:1237:1997:0:1:1
#
# Becomes
# @D3LH75P1:3:1101:1237:1997:0/1
#
# Read 2
# @D3LH75P1:3:1101:1237:1997:0:2:1
#
# Becomes
# @D3LH75P1:3:1101:1237:1997:0/2

import argparse
import gzip

def parse_args():
    """
    Parses the command line arguments.
    """
    parser = argparse.ArgumentParser(description='Fix fastq headers.')
    parser.add_argument('input', type=str, help='Input FASTQ - gzip accepted')
    parser.add_argument('output', type=str, help='Output FASTQ')

    return parser.parse_args()

def open_file(path):
	"""
	Obtain file handle if gzipped or normal text file.
	"""
	if path.endswith('.gz'):
	    return gzip.open(path, 'rt')

	return open(path, 'r')

def is_read1(file_path):
	"""
	Infer read from file name. Our files have read1 or read2 explicitly.
	"""
	return "read1" in file_path

def main():
	args = parse_args()
	read1 = is_read1(args.input)

	with open_file(args.input) as f:
		with open(args.output, "w") as w:
			for line in f:
				new_line = line

				if line.startswith("@D3LH75P1"):
					data = line.strip().split(":")
					del data[-2:]					
					new_line = ":".join(data)
					
					if read1:
						new_line = new_line + "/1"
					else:
						new_line = new_line + "/2"

					new_line = new_line + "\n"

				w.write(new_line)

if __name__ == "__main__":
	main()
```
</body></html>