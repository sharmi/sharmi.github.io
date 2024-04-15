---
 
title: 'Coreference Resolution Tools : A First Look'
pubDate: 2010-09-28 12:23:26.000000000 +05:30

category:  Natural Language Processing
tags:
- python
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2899453046'
author:  sharmi
 
canonical: "https://www.minvolai.com/coreference-resolution-tools-a-first-look/"
slug: coreference-resolution-tools-a-first-look
---
<p>Coreference is where two or more noun phrases refer to the same entity.   This is an integral part of natural languages to avoid repetition, demonstrate possession/relation etc.</p>
<p>Eg:&nbsp; <span style="color: #ff0000;">Harry</span> wouldn’t bother to read “<span style="color: #00ff00;">Hogwarts: A History</span>” as long as <span style="color: #0000ff;">Hermione</span> is around.&nbsp; <span style="color: #ff0000;">He</span> knows<span style="color: #0000ff;"> she</span> knows <span style="color: #00ff00;">the book</span> by heart.</p>
<p>The different types of coreference includes:<br />
Noun phrases: Hogwarts A history &lt;- the book<br />
Pronouns : Harry &lt;- He<br />
Possessives : her, his, their<br />
Demonstratives:  This boy</p>
<p>Coreference resolution or anaphor resolution is determining what an entity is refering to.  This has profound applications in nlp tasks such as semantic analysis, text summarisation, sentiment analysis etc.</p>
<p>In spite of extensive research, the number of tools available for CR and level of their maturity is much less compared to more established nlp tasks such as parsing.  This is due to the inherent ambiguities in resolution.</p>
<p>The following are some of the tools currently available.   Almost all tools come with bundled sentence deducters, taggers, parsers, named entity recognizers  etc as setting them up all would be tedious.</p>
<table>
<tbody>
<tr>
<td valign="top" width="200"><strong>Tools</strong></td>
<td valign="top" width="200"><strong>Demo</strong></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="200"><a href="http://cogcomp.cs.illinois.edu/page/software_view/18">Illinois Coreference Package</a></td>
<td valign="top" width="200"><a href="http://cogcomp.cs.illinois.edu/demo/coref/">http://cogcomp.cs.illinois.edu/demo/coref/</a></td>
</tr>
<tr>
<td valign="top" width="200"><a href="http://www.hlt.utdallas.edu/%7Ealtaf/cherrypicker.html">CherryPicker</a></td>
<td valign="top" width="200"></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="200">Natural Language Synergy Lab</td>
<td valign="top" width="200"><a href="http://nlp.i2r.a-star.edu.sg/demo_coref.html">http://nlp.i2r.a-star.edu.sg/demo_coref.html</a></td>
</tr>
<tr>
<td valign="top" width="200"><a href="http://www.bart-coref.org/">Bart</a></td>
<td valign="top" width="200"></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="200"><a href="http://aye.comp.nus.edu.sg/%7Eqiu/NLPTools/JavaRAP.html">JavaRAP</a></td>
<td valign="top" width="200"><a href="http://www-appn.comp.nus.edu.sg/%7Erpnlpir/cgi-bin/JavaRAP/JavaRAPdemo.html">http://www-appn.comp.nus.edu.sg/%7Erpnlpir/cgi-bin/JavaRAP/JavaRAPdemo.html</a></td>
</tr>
<tr>
<td valign="top" width="200"><a href="http://cswww.essex.ac.uk/Research/nle/GuiTAR/">GuiTAR</a></td>
<td valign="top" width="200"></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="200"><a href="http://opennlp.sourceforge.net/">OpenNLP</a></td>
<td valign="top" width="200"></td>
</tr>
<tr>
<td valign="top" width="200"><a href="http://www.cs.utah.edu/nlp/reconcile/">Reconcile</a></td>
<td valign="top" width="200"></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="200"><a href="http://www.ark.cs.cmu.edu/ARKref/">ARKref</a></td>
<td valign="top" width="200"><a title="http://www.ark.cs.cmu.edu/ARKref/" href="http://www.ark.cs.cmu.edu/ARKref/#ex">http://www.ark.cs.cmu.edu/ARKref/#ex</a></td>
</tr>
</tbody>
</table>
<p>Let us try using the following sentence from one of the presentations on BART as input. I'm using the demo app wherever possible and where not, I'm installing the same on my local machine.</p>
<p>Queen Elizabeth set about transforming her husband, King George VI, into a viable monarch. Lionel Logue, a renowned speech therapist, was summoned to help the King overcome his speech impediment.</p>
<p>The following equivalence sets need to be identified.</p>
<p>QE: Queen Elizabeth, her<br />
KG: husband, King George VI, the King, his<br />
LL: Lionel Logue, a renowned speech therapist</p>
<p>The results are as follows.</p>
<table>
<tbody>
<tr>
<td valign="top" width="88"><strong>Tools </strong></td>
<td valign="top" width="333"><strong>Result </strong></td>
<td valign="top" width="177"><strong>Comments</strong></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="88">Illinois Coreference Package</td>
<td valign="top" width="333"><span style="color: #008000;">Lionel Logue(0)<br />
a renowned speech |therapist|(2)<br />
his(8)</span><br />
<span style="color: #0000ff;">Queen Elizabeth(3)</span><br />
<span style="color: #ff0080;">the |King|(5)<br />
King(4)<br />
</span><span style="color: #800080;">King |George VI|(7)<br />
transforming her |husband|(6)<br />
her(1)</span><br />
<span style="color: #ff0000;">a viable |monarch|(9)</span></td>
<td valign="top" width="177">The ‘his’ and ‘her’ are wrongly matched to the wrong entities.&nbsp; his is matched to Logue and her is matched to King George</td>
</tr>
<tr>
<td valign="top" width="88">CherryPicker</td>
<td valign="top" width="333">&lt;COREF ID=”1″&gt;Queen&lt;/COREF&gt; &lt;COREF ID=”2″&gt;Elizabeth&lt;/COREF&gt; set about transforming &lt;COREF ID=”3″ REF=”2″&gt;her&lt;/COREF&gt; &lt;COREF ID=”4″&gt;husband&lt;/COREF&gt;, &lt;COREF ID=”5″&gt;King&lt;/COREF&gt; &lt;COREF ID=”6″ REF=&nbsp;&nbsp;&nbsp; “5″&gt;George VI&lt;/COREF&gt;, into a viable monarch.<br />
&lt;COREF ID=”9″&gt;Lionel Logue&lt;/COREF&gt;, a renowned speech &lt;COREF ID=”10″&gt;therapist&lt;/COREF&gt;, was summoned to help the &lt;COREF ID=”7″ REF=”5″&gt;King&lt;/COREF&gt; overcome &lt;COREF ID=”8″ REF=”5″&gt;his&lt;/COREF&gt; sp eech impediment.</p>
<p><span style="color: #0000ff;">Queen</span><br />
     <span style="color: #0000ff;">&nbsp;&nbsp;&nbsp;&nbsp;her</span><br />
     <span style="color: #808000;">Elizabeth</span><br />
     <span style="color: #ff8000;">Husband</span><br />
     <span style="color: #ff0000;">King</span><br />
     <span style="color: #ff0000;">&nbsp;&nbsp;&nbsp;&nbsp;George VI</span><br />
     <span style="color: #ff0000;">&nbsp;&nbsp;&nbsp;&nbsp;King</span><br />
     <span style="color: #ff0000;">&nbsp;&nbsp;&nbsp;&nbsp;his</span><br />
     <span style="color: #800000;">Lionel Logue</span><br />
     <span style="color: #008000;">Therapist</span>
     </td>
<td valign="top" width="177">It is mostly ok, except that Queen Elizabeth is split into two Entities Queen and Elizabeth .&nbsp; Other than that, it is one of the best results.&nbsp; Notably it matches King to King George VI and hence, his is correctly mapped to King George VI</td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="88">Natural Language Synergy Lab</td>
<td valign="top" width="333"><span class="bBlue"><span style="color: #0000ff;"><sup><strong>0</strong></sup> Queen Elizabeth</span> </span>set about transforming <span class="bBlue"><span style="color: #0000ff;"><sup><strong>0</strong></sup> her</span> </span>husband , King George VI , into a viable monarch. Lionel Logue , a renowned speech therapist , was summoned to help the King overcome <span class="bBlue"><span style="color: #0000ff;"><sup><strong>0</strong></sup> his</span> </span>speech impediment .</td>
<td valign="top" width="177"></td>
</tr>
<tr>
<td valign="top" width="88">BART</td>
<td valign="top" width="333">
<div id="coref" class="minidisc"><span class="coref-active">{<sub class="chunk">person</sub> Queen Elizabeth } </span>set about transforming {<sub class="chunk">np</sub> <span class="coref-active" onclick="set_activeset('set_1')">{<sub class="chunk">np</sub> her } </span>husband } , {<sub class="chunk">person</sub> King George VI } , into <span class="coref-active" onclick="set_activeset('set_1')">{<sub class="chunk">np</sub> a viable monarch } </span>. <span class="coref-inactive" onclick="set_activeset('set_0')">{<sub class="chunk">person</sub> Lionel Logue } </span>, <span class="coref-inactive" onclick="set_activeset('set_0')">{<sub class="chunk">np</sub> a renowned <span class="coref-inactive" onclick="set_activeset('set_2')">{<sub class="chunk">np</sub> speech } </span>therapist } </span>, was summoned to help <span class="coref-active" onclick="set_activeset('set_1')">{<sub class="chunk">np</sub> the King } </span>overcome {<sub class="chunk">np</sub> <span class="coref-active" onclick="set_activeset('set_1')">{<sub class="chunk">np</sub> his } </span><span class="coref-inactive" onclick="set_activeset('set_2')">{<sub class="chunk">np</sub> speech } </span>impediment } .</div>
<h4>Coreference chain 1</h4>
<div id="coref-chain" class="minidisc">
<div>{person Queen Elizabeth }</div>
<div>{np her }</div>
<div>{np a viable monarch }</div>
<div>{np the King }</div>
<div>{np his }</div>
<div></div>
</p></div>
<h4>Coreference chain 2</h4>
<div id="coref-chain" class="minidisc">
<div>{person Lionel Logue }</div>
<div>{np a renowned {np speech } therapist }</div>
<h4>Coreference chain 3</h4>
<div id="coref-chain" class="minidisc">
<div>{np speech }</div>
<div>{np speech }</div>
</p></div>
</p></div>
</td>
<td valign="top" width="177"></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="88">JavaRAP</td>
<td valign="top" width="333">********Anaphor-antecedent pairs*****<br />
     (0,0) Queen Elizabeth &lt;– (0,5) her,<br />
     (1,12) the King &lt;– (1,15) his<br />
     ********Text with substitution*****<br />
     Queen Elizabeth set about transforming &lt;Queen Elizabeth’s&gt; husband, King George VI, into a viable monarch.<br />
     Lionel Logue, a renowned speech therapist, was summoned to help the King overcome &lt;the King’s&gt; speech impediment.</td>
<td valign="top" width="177">It has attempted only the pronoun resolution and that has been done well.</td>
</tr>
<tr>
<td valign="top" width="88">GuiTAR</td>
<td valign="top" width="333">Failed</td>
<td valign="top" width="177"></td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="88">OpenNLP</td>
<td valign="top" width="333">(TOP (S (NP#6 (NNP Queen) (NNP Elizabeth)) (VP (VBD set) (PP (IN about) (S (VP (VBG transforming) (NP (NP (NML#6 (PRP$ her)) (NN husband)) (, ,) (NP (NNP King) (person (NNP George) (NNP VI)))) (, ,) (PP (IN into) (NP (DT a) (JJ viable) (NN monarch))))))) (. .)) )<br />
     (TOP (S (NP#1 (NP (person (NNP Lionel) (NNP Logue))) (, ,) (NP#1 (DT a) (JJ renowned) (NN speech) (NN therapist))) (, ,) (VP (VBD was) (VP (VBN summoned) (S (VP (TO to) (VP (VB help) (S (NP (DT the) (NNP King)) (VP (VBN overcome) (NP (NML#1 (PRP$ his)) (NN speech) (NN impediment))))))))) (. .)) )
<p>
     Lionel Logue<br />
     a renowned speech therapist<br />
     Queen Elizabeth<br />
     her husband</p>
</td>
<td valign="top" width="177"></td>
</tr>
<tr>
<td valign="top" width="88">Reconcile</td>
<td valign="top" width="333">&lt;NP NO=”0″ CorefID=”1″&gt;Queen Elizabeth&lt;/NP&gt; set about transforming &lt;NP NO=”2″ CorefID=”3″&gt;&lt;NP NO=”1″ CorefID=”1″&gt;her&lt;/NP&gt; husband&lt;/NP&gt;, &lt;NP NO=”3″ CorefID=”3″&gt;King George VI&lt;/NP&gt;, into &lt;NP NO=”&nbsp;&nbsp;&nbsp; 4″ CorefID=”4″&gt;a viable monarch&lt;/NP&gt;. &lt;NP NO=”5″ CorefID=”6″&gt;Lionel Logue&lt;/NP&gt;, &lt;NP NO=”6″ CorefID=”6″&gt;a renowned speech therapist&lt;/NP&gt;, was summoned to help &lt;NP NO=”7″ CorefID=”6″&gt;the King&lt;/NP&nbsp;&nbsp;&nbsp; &gt; overcome &lt;NP NO=”9″ CorefID=”9″&gt;&lt;NP NO=”8″ CorefID=”6″&gt;his&lt;/NP&gt; speech impediment&lt;/NP&gt;.
<p>
     <span style="color: #000080;">Queen Elizabeth<br />
     her<br />
     </span><span style="color: #ff8000;">her husband<br />
     King George VI<br />
     </span><span style="color: #400000;">A Viable monarch</span><br />
     <span style="color: #008000;">Lionel Logue<br />
     a renowned speech therapist<br />
     the king<br />
     his</span></p>
</td>
<td valign="top" width="177">the king has been wrongly attributed to Lionel Logue, which resulted in his also to be wronlt atttributed.</td>
</tr>
<tr class="alt-table-row">
<td valign="top" width="88">ARKref</td>
<td valign="top" width="333"><span class="non_singleton" style="color: maroon;"><span class="bracket">[</span><span class="text">Queen Elizabeth</span><span class="bracket">]</span><span class="entityid">1</span></span><span class="text"> set about transforming </span><span class="non_singleton" style="color: navy;"><span class="bracket">[</span><span class="non_singleton" style="color: maroon;"><span class="bracket">[</span><span class="text">her</span><span class="bracket">]</span><span class="entityid">1</span></span><span class="text"> husband , </span><span class="non_singleton" style="color: navy;"><span class="bracket">[</span><span class="text">King George VI</span><span class="bracket">]</span><span class="entityid">2</span></span><span class="text"> ,</span><span class="bracket">]</span><span class="entityid">2</span></span><span class="text"> into </span><span class="singleton"><span class="bracket">[</span><span class="text">a viable monarch</span><span class="bracket">]</span></span><span class="text"> .<br />
     </span><span class="non_singleton" style="color: green;"><span class="bracket">[</span><span class="text">Lionel Logue , </span><span class="non_singleton" style="color: green;"><span class="bracket">[</span><span class="text">a renowned speech therapist</span><span class="bracket">]</span><span class="entityid">6</span></span><span class="text"> ,</span><span class="bracket">]</span><span class="entityid">6</span></span><span class="text"> was summoned to help </span><span class="non_singleton" style="color: orange;"><span class="bracket">[</span><span class="text">the King</span><span class="bracket">]</span><span class="entityid">8</span></span><span class="text"> overcome </span><span class="singleton"><span class="bracket">[</span><span class="non_singleton" style="color: orange;"><span class="bracket">[</span><span class="text">his</span><span class="bracket">]</span><span class="entityid">8</span></span><span class="text"> speech impediment</span><span class="bracket">]</span></span><span class="text"> .<br />
     </span></td>
<td valign="top" width="177">One of the best results.&nbsp; only info lacking is linking ‘the King’ to “king George VI”</td>
</tr>
</tbody>
</table>
<blockquote>
<p>_As a side note, cherry picker fails with the following error.</p>
<p>  cherrypicker1.01/tools/crf++/.libs/lt-crf_test: error while loading shared libraries: libcrfpp.so.0: cannot open shared object file: No such file or directory</p>
<p>  To proceed, we need to download <a href="http://crfpp.sourceforge.net/">CRF++</a> .  Install it.</p>
<p>  Then we need to modify line no 18 in cherrypicker.sh file<br />
  tools/crf++/crf_test -m modelmd $1.crf > $1.pred<br />
  to<br />
  crf_test -m modelmd $1.crf > $1.pred</p>
<p>  open a new terminal:<br />
  run<br />
  sudo ldconfig_</p>
<p>  now cherrypicker should work</p>
</blockquote>
<p>ARKref and Cherrypicker seem to be the best options available right now.</p>
<p>Are there any other coreference resolution systems that have not been looked at?  Can we add more about the above tools?  Please post your comments.</p>
