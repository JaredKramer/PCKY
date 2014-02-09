PCKY
====

PCKY Parser

This is a Probabalistic Cocke-Kasami-Younger (PCKY) Parser. It takes as input a treebank in Chomsky Normal Form, from which it produces a probablistic grammar. Given a list of sentences, the program creates lists of potential parses using that grammar. It then outputs the most likely parse for each sentence. The second, third etc... most likely parses are also calculated but not produced in the output. On my local machine, this program outputs the most probable parses for 50 sentences in roughly .5 seconds.

This program makes use of the Natural Language ToolKit (NLTK) for Python. Specifically, it uses the nltk.tree.Tree data structure to keep track of the parses as they are calculated. 


Usage:
The command line arguments are as follows:
1 = the treebank used to train the grammar
2 = the sentences to be parsed
3 = the file to which the grammar is written
4 = the file to which the most likely parse for each sentence is written
