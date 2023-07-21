---
 
title: Metonym - Graph based WordNet Similarity Estimator
publishDate: 2012-02-06 12:43:30.000000000 +05:30

category:  Uncategorised
tags: []
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2918203289'
author:  sharmi
 
canonical: "https://www.minvolai.com/metonym-graph-based-wordnet-similarity-estimator/"
slug: metonym-graph-based-wordnet-similarity-estimator
---
<p>Open Source world often provides us treasure troves of golden libraries. Only you might have to spend some time mining for the nuggets. WordNet is such a nugget, if there ever was one. So many words and their senses are catalogued often along with their inherent structure. A definition for each word sense and plenty of examples are also provided. Only what is missing is a well rounded algorithm for similarity comparison for words.</p>
<h3>The Issue</h3>
<p>WordNet finds the similarity of two word senses by using the hypernym and hyponym relations of nouns and the verb group property of verbs. Such method has certain disadvantages.  Word sense similarity measure is not available for adverbs and adjectives.</p>
<p>Similarity measure is restricted to within a POS group.<br />
 &#42;POS -&#62; Part Of Speech</p>
<h3>Metonym</h3>
<p>Metonym addresses the limitations in python WordNet’s default implementation for measuring the similarity of two word senses. It constructs a graph of all word senses using a horde of relations like attributes, causes, derivationally related forms, topic domains, hypernyms and hyponyms etc. It is composed of 117659 nodes and 155165 edges. The measure of similarity between two senses is a function of the shortest path distance between them in the wordnet graph.</p>
<h3>Working with Metonym</h3>
<p>To install with Metonym:</p>
<pre><code class="python">    pip install metonym

</code></pre>
<p>To create the graph:</p>
<pre><code class="python">    create_wordnet_graph /path/to/wordnet_graph.adj

</code></pre>
<p>This creates a wordnet graph at /path/to/wordnet&#95;graph.adj . Its roughly 4.1 Mb in size.</p>
<p>You can use the test/test&#95;metonym.py to try out comparison of words.</p>
<pre><code class="python">    from nltk.corpus import wordnet as wn
    from metonym import WordNetGraph
    from argparse import ArgumentParser

    def main(graph_file, word1, word2):
        wngraph = WordNetGraph(graph_file)
        syns1 = wn.synsets('beautiful')
        syns2 = wn.synsets('beauty')
        print "\n===The meaning of each word sense==="
        for syn in syns1 + syns2:
            print "%s\t%s\t%s" %(syn.name, ":", syn.definition)
        print "\n===Comparing synset of one term with the synset of another==="
        for syn1 in syns1:
            for syn2 in syns2:
                print "%s

</code></pre>
