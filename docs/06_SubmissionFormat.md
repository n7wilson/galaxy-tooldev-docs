#Submission File Formats

Below are the formatting requirements for your submission files for each subchallenge. This is the format that the Evaluator tool in Galaxy will expect for each Challenge. If you submit a file that does not follow the specified format the Evaluator will throw an error and the offending file will not be evaluated.

${toc}


#Sub-Challenge 1

##Sub-Challenge 1A: Tumour cellularity
The submission file must be a single line, containing in plain text, a real number >= 0 and <= 1, representing an estimate of the proportion of cells in the sample that are cancerous.

####!Examples
__Correct Example__
```
0.84
```
__Incorrect Example__
```
-0.7
```

##Sub-Challenge 1B: Number of cancerous populations/clusters of mutations:
The submission file must be a single line, containing in plain text, an integer number >= 1 and <= 20, representing the number of cancerous populations present in the sample.

####!Examples
__Correct Example__
```
4
```
__Incorrect Examples__
```
6.5
```
```
24
```
```
4.0
```

##Sub-Challenge 1C: Mutation cluster sizes and cellular proportions
The submission file must be a 3-columned text file with columns separated by tabs.
* Rows:
      * The file must contain a between 1 and 20 lines, each representing a cancerous population/cluster of mutations.  __If participating in Sub Challenge 1B, the number of lines must be the same as the Sub Challenge 1B entry.__
* Columns
      1. The row number, which represents the cluster ID.
      2. A positive integer, representing the number of SSMs assigned to that cluster.
      3. A real number >= 0 and <= 1 representing a best guess for the proportion of cells in the sample containing the mutations in the cluster. Note: these proportions __do not__ need to to sum to 1.
__The sum of the second column must equal the total number of SSMs in the sample. This number is found in the specification file for Sub Challenge 1.__

####!Examples
*Assuming there are 4 SSMs in the sample*
__Correct Example__
```
1	2	.9
2	2	.5
```
__Incorrect Examples__
```
# Incorrect row number
0	4	.9
```
```
# Incorrect row number
1	2	.9
3	2	.5
```
```
# Non-integer number of SSMs
1	2.5	.9
2	2	.5
```
```
# Cell proportion is > 1
1	2	1.4
2	2	-0.5
```
```
# Number of SSMs assigned to clusters > number of SSMs in the sample
1	2	.9
2	4	.5
```

#Sub-Challenge 2

##Sub-Challenge 2A: Assigning mutations to clusters.
The submission file must be a 1-columned text file with a number of lines equal to the number of SSMs in the VCF file.
Each line contains the ID of the cluster that that SSM belongs to. The cluster ID's used must all be positive, sequential integers starting with 1.
####!Examples
*Assuming there are 4 SSMs in the sample*
__Correct Example__
```
1
2
1
1
```
__Incorrect Examples__
```
# Incorrect number of columns
1_1	1
1_2	2
2_1	1
3_1  1
```
```
# Not enough rows
1
2
1
```
```
# Non-integer cluster ID
1
2
1.5
1
```
```
# Cluster 3 exists but cluster 2 does not
1
3
1
1
```

##Sub-Challenge 2B: Co-Clustering Matrix
The submission file must be a text file containing an NxN matrix Co-Clustering Matrix (CCM) where N is the number of SSMs.  Each entry CCM~i,j~ must be a real number >= 0 and <= 1 representing the probability that SSM i and SSM j are in the same cluster.
Diagonal entries must be 1.
The matrix must be symmetric.
*We recommend using a maximum of 4 digits after the decimal space to save space*
####!Examples
*Assuming there are 4 SSMs in the sample*
__Correct Example__
```
1	0	0.7	0
0	1	0	1
0.7	0	1	0.2
0	1	0.2	1
```
__Incorrect Examples__
```
# Incorrect size
1	0	0.7
0	1	0
0.7	0	1
```
```
# Non-symmetric matrix
1	0	1	1
0	1	0	1
1	0	1	0
0	1	0	1
```
```
# Diagonal is not all 1's
1	0	1	0
0	0	0	1
1	0	1	0
0	1	0	1
```
```
# Entries are not between 0 and 1
1	0	-0.2	0
0	1	0	1.5
-0.2	0	1	0
0	1.5	0	1
```

#Sub-Challenge 3
For this sub-challenge two files must be submitted. The first is of the same form as sub-challenge 2A or 2B above (depending on whether you are submitting to sub-challenge 3A or 3B respectively) containing information on the co-clustering of mutations. The second file contains information on the phylogenetic relationship of clusters and details are found below.

##Sub-Challenge 3A: Assigning clusters to a phylogeny
The second submission file must be a 2-columned text file with columns separated by tabs with a number of lines equal to the number of cancer populations/clusters of mutations.
* Columns
    1. The row number, which represents the cluster ID.
    2. The cluster ID of the parent of the cluster.  To represent the root of the phylogenic tree, the germline node, use cluster ID 0 for the parent cluster ID.
The tree must be fully connected and each node can have only a single parent.
__The number of rows must be equal to the number of clusters used in the co-clustering file submitted for this challenge. This co-clustering file is of the same format as the submission file for sub-challenge 2A.__
####!Examples
*Assuming there are 4 SSMs in the sample and 2 clusters in the co-clustering file*
__Correct Example__
```
1	0
2	1
```
Phylogenetic Tree (where each circle represents a cluster and the number inside each circle is the cluster ID):
${image?fileName=phylogeny_tree1.png}
__Incorrect Examples__
```
# Incorrect number of clusters
1	0
2	1
3   1
```
Phylogenetic Tree:
${image?fileName=phylogeny_tree_incorrect1.png}
```
# Incorrect cluster numbering
0	0
2	1
```
Phylogenetic Tree:
${image?fileName=phylogeny_tree_incorrect2.png}
```
# Tree is not connected (two root nodes)
1	0
2	0
```
Phylogenetic Tree:
${image?fileName=phylogeny_tree_incorrect3.png}

##Sub-Challenge 3B: Ancestor - Descendent matrix
The second submission file must be a text file containing an NxN Ancestor-Descendant Matrix (ADM), where N is the number of mutations.  Each entry ADM~i,j~ represents the probability that SSM i is in a lineage that is an ancestor of the lineage containing SSM j.
Each entry must be a real number >= 0 and <= 1.
Diagonal entries must be 0.
__For every entry i, j: ADM~i,j~ + CCM~i,j~ <= 1 where CCM = co-clustering matrix from the co-clustering file submitted for this challenge. This co-clustering file is of the same format as the submission file for sub-challenge 2A.__
*We recommend using a maximum of 4 digits after the decimal space to save space*
####!Examples
*Assuming there are 4 SSMs in the sample and the following matrix is submitted for the co-clustering matrix:*
```
# Co-Clustering Matrix (CCM)
1	0	0.7	0
0	1	0	1
0.7	0	1	0.2
0	1	0.2	1
```
__Correct Example__
```
0	1	0.3	1
0	0	0	0
0	0.5	0	1
0	0	0	0
```
Phylogenetic Tree (where each circle represents a cluster and the numbers inside each circle are the mutations associated with that cluster):
${image?fileName=phylogeny_tree2.png}
__Incorrect Examples__
```
# Incorrect number of clusters
0	1	0.3
0	0	0
0	0.5	0
```
Phylogenetic Tree:
${image?fileName=phylogeny_tree_incorrect4.png}
```
# Diagonal entries are not 0's
0	1	0.3	1
0	1	0	0
0	0.5	1	1
0	0	0	0
```
Phylogenetic Tree:
${image?fileName=phylogeny_tree_incorrect5.png}
```
# Entries are not between 0 and 1
0	1.2	0.3	1
0	0	0	0
0	0.5	0	-1
0	0	0	0
```
Phylogenetic Tree:
${image?fileName=phylogeny_tree_incorrect6.png}
```
# ADM + CCM has entries > 1
0	1	0.5	1
0	0	0	0
0	0.5	0	1
0	0.1	0	0
```
Phylogenetic Tree:
${image?fileName=phylogeny_tree_incorrect7.png}

# Testing Your Submission in Galaxy

Once you have created your submission tool in Galaxy you can test your tool using the Training Sets provided. To do this you need to run the Training Set VCF through your tool then take the outputted submission files for each sub-challenge and run them through the provided evaluator. For details on how this is done check out the SMC-Het-Development video in Section 3.5.0 - Tutorials.

## Errors

It is possible that one or more of your sub-challenge submissions will cause errors in the evaluator due to incorrect formatting or invalid values in you submission file. If this happens the Galaxy job that evaluates your submission will report an error and you can view the cause of this error by clicking on the 'View or report this error' button:
${image?fileName=galaxy_debugger_button.png}
in your job history and looking at the function output.

${image?fileName=galaxy_error_info.png}

You can also view the results from any successfully evaluated sub-challenge submissions by clicking on 'View data' button:
${image?fileName=galaxy_view_data_button.png}
in your job history.

${image?fileName=galaxy_view_data.png}