---
 
title: Email Classification - Supervised Learning
pubDate: 2012-01-29 12:32:23.000000000 +05:30

category:  Machine Learning
tags: []
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2897697608'
author:  sharmi
 
canonical: "https://www.minvolai.com/email-classification-supervised-learning/"
slug: email-classification-supervised-learning
---
<p><strong>Background:  </strong><a href="http://localhost:10003/blog/2012/01/learning-to-data-crunch-my-foray-in-machine-learning/">Learning to Data Crunch</a>. The first challenge I set for myself was email classification. While spam classification might seem like beating a dead horse. some of my reasons for choosing it is as below</p>
<ul>
<li>It had to be based on text processing as that what is most of my personal projects are based on.</li>
<li>This is the ideal candidate for a beginner as it a well worked on problem. Hence getting a huge corpus with truckloads of them tagged was very easy.</li>
</ul>
<p>To make it more interesting and challenging, the target of this experiment will aimed at comparing the performance of supervised learning with semi-supervised learning.</p>
<p>If you find what I have written below to be a bit incoherent, thats because these were written as I progressed in solving the problem, rather than after it was done.</p>
<p>Corpus Stats</p>
<ol>
<li>The corpus is from <a href="http://plg1.cs.uwaterloo.ca/cgi-bin/cgiwrap/gvcormac/foo">TREC 2005 Spam Public Corpora</a>.</li>
<li>It has a total of 92,189 Messages.</li>
<li>The total number of spam and ham is 52790 and 39399 respectively.</li>
</ol>
<h2>The Idea</h2>
<p>2000 messages are selected at random for test (1000 spam, 1000 ham) and 2000 messages are selected at random for validation.  I’m going to use the same training corpus to run a supervised learning process and a semi supervised learning algo and see how the performance of the semi supervised learning algo measures up to the supervised learning algo.</p>
<p><strong>Supervised Learning</strong> 88,189 messages as training</p>
<p><strong>Semisupervised Learning</strong> Treat Initial batch of 5000 as tagged and then work through the data as unlabelled batches of 5000 each.</p>
<p>The features I plan to use besides the bag of words method 1. html or plaintext 2. attached images count 3. attached videos count 4. attached applications count</p>
<h2>Implementing Supervised Learning Algorithm</h2>
<p>The initial number of features were greater than 4.4L or 0.44M This is was mostly bag of words besides the number of images, applications or videos attached in the content.</p>
<p>I reduced the features further by removing any word that was not repeated in other documents.</p>
<p>The number of emails = 88189 Number of features = 190562 Assuming I need 1 byte to store a feature per email, it would result in 16805472218 bytes or 16800G approx :)</p>
<p>Refining further, let’s remove any email address, url or number from the vocabulary. This will reduce 58201, 7446 and 6417 terms respectively. Thus the terms come down to 123286. Sparse Matrix to the rescue!</p>
<p>Using a sparse matrix from scipy greatly improves the performance. I have chosen Support Vector Classifier and it uses the rbf kernel.</p>
<h2>Execution</h2>
<p>The only input required is datadir - the location of the untarred dataset /path/to/trec05p–1 .</p>
<p><code>python process_email.py $datadir</code></p>
<p><strong>process_email.py</strong> parses the email and stores the extracted data in the database for later processing.</p>
<p><code>python create_data_sets.py $datadir</code></p>
<p><strong>create_data_sets.py</strong> splits the email_index into training sets(supervised and semisupervised), validation and testing email_index.</p>
<p><code>python supervisor_learn.py $datadir</code></p>
<p><strong>supervisor_learn.py</strong> trains the supervised model.</p>
<p>The code is hosted at <a href="https://github.com/sharmi/emailspam_ml">https://github.com/sharmi/emailspam_ml</a></p>
<p>The supervised model is trained with C=1 and gamma=0.001. The error rate on predicting the test set is 6.35%. I’m directly using the error rate as  the measure as the spam and ham messages are nearly equal in distribution.</p>
<p>I suppose these are good results. Currently the grid search for combinations of C and gamma is in progress. The rate at which it is going, it is will be days before it completes. Once it completes, I will post the results.</p>
