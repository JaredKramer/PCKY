#!/usr/bin/env python2.7
# -*- coding: utf-8 -*-
"""
@author: Jared Kramer and T.J. Trimble
"""

import sys
from nltk.tree import Tree
from collections import defaultdict, Counter as counter
import time
start_time = time.time()

if len(sys.argv) > 1:
    train, sentences, grammar_out, parses = sys.argv[1:]
else:
    train = "../data/parses.train"
    sentences = "../data/sents.test"
    grammar_out = "trained.improved.pcfg"
    parses = "parses.improved.hyp"

# initialize and load in data
grammar = defaultdict(list)
train = open(train).read().strip().split("\n")
sentences = open(sentences, 'r').read().strip().split("\n")

for s in train:
    for rule in Tree(s).productions():
        grammar[rule.lhs()].append(rule.rhs())

# create pcfg the key is the lhs of the rule,
# the value is a dictionary where the key is a tuple of the RHS
# and the value is the prob for that RHS
pcfg = defaultdict(dict)
for left in grammar:
    for k, v in counter(grammar[left]).most_common():
        pcfg[left][k] = v/float(len(grammar[left]))

inversePCFG = defaultdict(list)

for key in pcfg:
    for value in pcfg[key]:
        inversePCFG[value].append((key, pcfg[key][value]))

#output trained grammar
grammar_out = open(grammar_out, 'w')
for left in pcfg:
    for right in pcfg[left]:
        grammar_out.write(" ".join([str(left), "->", " ".join([str(item) for item in right]), "["+str(pcfg[left][right])+"]", "\n"]))
grammar_out.close()

# This method helps fill in the bottom of each row in the parse table
def find_nts(word):
    values = inversePCFG[(word,)]
    result = [(Tree(item[0], [word, '']), item[1]) for item in values]
    return result

parses = open(parses, 'w')
previous = time.time()
numberOfParses = 0
for sentence in sentences:
    print sentence
    table = defaultdict(lambda: defaultdict(list))
    # Sentences already tokenized
    sentence = sentence.split()
    length = len(sentence)
    for end in range(2, length+2):
        table[end-2][end-1] = find_nts(sentence[end-2]) # Gather a list of terminal tuples in the bottom of each column
        for start in range(end-2, -1, -1):
            for split in range(start+1, end):
                for left in table[start][split]: # list of tree, float tuples
                    for right in table[split][end-1]:
                        rules = inversePCFG[(left[0].node, right[0].node)]
                        table[start][end-1].extend([(Tree(item[0], [left[0], right[0]]), left[1]*right[1]*item[1]) for item in rules])

    if len(table[0][length]) == 0:
        parses.write("\n")
        print "Fail",
    else:
        numberOfParses += 1
        parse = sorted(table[0][length], key=lambda x: x[1])[-1]
        parses.write("".join([parse[0].pprint(1000000).replace(" )", ")"), "\n"]))
        print "Success",
    print time.time() - previous
    previous = time.time()
    print

parses.close()

print numberOfParses, "of", len(sentences), "parses;", "%%%.2f" % (numberOfParses*100/float(len(sentences)))
print "%.4f seconds" % (time.time() - start_time)
