---
title: "How to install VEP on focal"
tags: [VEP focal ubuntu]
layout: post
---
Despite it's 2020, we still have issues in installing standard software such as vep. On Focal (ubuntu distribution), do the following

1. Install following libraries
	1. libdbd-mysql-perl
	2. libdbi-perl
	3. libarchive-zip-perl
2. Run the command 
	1. cpan Module::Build (This would give you many options, I chose **sudo**)
3. Now clone the vep repo
	1. Clone the vep repo (git clone https://github.com/Ensembl/ensembl-vep.git)
	2. Go to ensembl-vep repo
	3. You should see htslib directory. you can keep it if you are okay with it. I did step 4 and 5
	4. Delete the htslib
	5. Clone the htslib (git clone https://github.com/samtools/htslib.git)
4. Now execute the command
	1. perl INSTALL.pl

This should build VEP and asks you to download the cache and fasta (reference) files. They are huge. Do not download unless you are working in ivory towers. You can download the plugins though. Plugins are installed in your home directory/.vep. They are small.
