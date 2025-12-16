DATE: 16/12/2025
TIME SPENT: 3 Hours
TASKS COMPLETED: Completed building up a repository skeleton with the major files and their framework design. Set up environment variables in anaconda command prompt and use makeblastdb for 6 amr_seq_gene.Generated .nhr,.nin,.nsq files in the amr_gene.fasta folder.
BLOCKERS: Understanding the working of makeblastdb and how it works. How to perform concatenations using command terminal and run blastdb on a single fasta file to create .mhr, .nin, .nsq files.
EVIDENCE: (amr-finder) C:\Users\Lenovo\Downloads\amr-finder\amr_db\amr_genes.fasta>blastn -version
  blastn: 2.17.0+
  Package: blast 2.17.0, build Jul  1 2025 08:57:20
  (amr-finder) C:\Users\Lenovo\Downloads\amr-finder\amr_db\amr_genes.fasta>makeblastdb -in amr_all.fasta -dbtype nucl -out        amr_all_genes.fasta

  Building a new DB, current time: 12/17/2025 00:19:39
  New DB name:   C:\Users\Lenovo\Downloads\amr-finder\amr_db\amr_genes.fasta\amr_all_genes.fasta
  New DB title:  amr_all.fasta
  Sequence type: Nucleotide
  Keep MBits: T
  Maximum file size: 3000000000B
  Adding sequences from FASTA; added 6 sequences in 0.0042493 seconds.
