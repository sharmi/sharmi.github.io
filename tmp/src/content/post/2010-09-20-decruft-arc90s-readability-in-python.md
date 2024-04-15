---
 
title: 'Decruft: Arc90''s Readability in Python'
pubDate: 2010-09-20 11:59:14.000000000 +05:30

category:  python
tags: []
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
author:  sharmi
 
canonical: "https://www.minvolai.com/decruft-arc90s-readability-in-python/"
slug: decruft-arc90s-readability-in-python
---
<p>One of the pressing problems in web data extraction is separating meaningful content pertaining to the subject from cruft like navigation links, ads, footnotes and sidebar contents promoting links to other pages with the site or other sites.</p>
<p>This is crucial as the page would be processed for the right content and the signal/noise ratio would be highly reduced in the following processing stages. There have been extensive research in this area and many papers have been presented.</p>
<p>One of the significant steps forward has been <a href="http://lab.arc90.com/experiments/readability/">Arc90’s readability</a> project. Readability is a piece of javascript code that resides as a bookmarklet in your browser. When clicked/activated, it extracts the main article content, hiding rest of the cruft making it easy on your eye and your left brain.</p>
<h3>decruft</h3>
<p>As with any novel project Readability has been ported to php, ruby, python etc, propelled even more by the fact that javascript engines run code in a highly constrained environment and thus are hard to leverage. The python port, <a href="http://github.com/gfxmonk/python-readability">python-readability</a>, by gfx-monk is almost similar to readability in logic but is too slow.</p>
<p>decruft is a fork of python-readability to make it faster. It also has some logic corrections and improvements along the way.</p>
<p>python-readability uses BeautifulSoup to parse the html whereas decruft uses python-lxml.</p>
<p>The advantage of using BeautifulSoup is that it’s purely python and bundled with python-readability. Hence you do not have any outside dependency and can run on any platform, including google AppEngine.</p>
<p>The advantage of decruft is, as it uses libxml2, the processing is orders of magnitude faster and it can parse broken html that even BeautifulSoup cannot handle. But it needs lxml and hence restricted to linux systems.</p>
<p>Decruft is available at <a href="http://code.google.com/p/decruft/">google code</a>.</p>
<h3>Comparison</h3>
<p>Six Web pages with reasonable article-like content has been taken and tested for performance against python-readability and decruft. The time taken for each process is given in secs in the table below. It should also be noted that while BeautifulSoup had failed to parse parts of National Geographic due to malformed start tag, lxml had no issues.</p>
<p>(<em>In the arc90 links, readability will be applied after the page loads</em>.)</p>
<table border="2" width="547">
<tr>
<th>Original Page</th>
<th>Size</th>
<td><strong>Arc90’s</strong></td>
<th>python-readability</th>
<th>decruft</th>
</tr>
<tr>
<td><a href="http://localhost:10003/uploads/decruft-samples/asianunicorn.html" target="_blank">Endangered Asian &#8216;unicorn&#8217; captured, first sighting in decade &#8211; CNN.com</a></td>
<td>108.56K</td>
<td><a href="http://localhost:10003/uploads/decruft-samples/asianunicorn_arc90.html" target="_blank">link</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/readability_asianunicorn.html" target="_blank">15.9949080944</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/decruft2_asianunicorn.html" target="_blank">0.187147140503</a></td>
</tr>
<tr>
<td><a href="http://localhost:10003/uploads/decruft-samples/science-environment-11314446.html" target="_blank">BBC News &#8211; Cacao genome &#8216;may help produce tastier chocolate&#8217;</a></td>
<td>59.27K</td>
<td><a href="http://localhost:10003/uploads/decruft-samples/science-environment-11314446_arc90.html" target="_blank">link</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/readability_science-environment-11314446.html" target="_blank">12.1847000122</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/decruft2_science-environment-11314446.html" target="_blank">0.383187055588</a></td>
</tr>
<tr>
<td><a href="http://localhost:10003/uploads/decruft-samples/articspecies.html" target="_blank">Arctic species under threat, report warns &#8211; CNN.com</a></td>
<td>123.34K</td>
<td><a href="http://localhost:10003/uploads/decruft-samples/articspecies_arc90.html" target="_blank">link</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/readability_articspecies.html" target="_blank">16.9275019169</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/decruft2_articspecies.html" target="_blank">0.208026885986</a></td>
</tr>
<tr>
<td><a href="http://localhost:10003/uploads/decruft-samples/Nature.html" target="_blank">Nature &#8211; Wikipedia, the free encyclopedia</a></td>
<td>272.23K</td>
<td><a href="http://localhost:10003/uploads/decruft-samples/Nature_arc90.html" target="_blank">link</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/readability_Nature.html" target="_blank">151.132905006</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/decruft2_Nature.html" target="_blank">2.32351303101</a></td>
</tr>
<tr>
<td>National Geographic: <a href="http://localhost:10003/uploads/decruft-samples/Tyrannosaurs Were Human-size for 80 Million Years.html" target="_blank">Tyrannosaurs Were Human-size for 80 Million Years</a></td>
<td>134.14K</td>
<td><a href="http://localhost:10003/uploads/decruft-samples/Tyrannosaurs Were Human-size for 80 Million Years_arc90.html" target="_blank">link</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/readability_Tyrannosaurs Were Human-size for 80 Million Years.html" target="_blank">17.4031939507</a></td>
<td><a href="http://localhost:10003/uploads/decruft-samples/decruft2_Tyrannosaurs Were Human-size for 80 Million Years.html" target="_blank">0.997216939926</a></td>
</tr>
</table>
<h3>Improvements in decrufting logic</h3>
<p>The following are improvements in logic over readability/python-readability.</p>
<ol>
<li>More patterns for possibility of meaningful content are checked for in tags’ id and class tags.</li>
<li>A part of logic deems div’s that do not have containers like img, table, div etc as children as paragraph tags. python-readability (probably as a typo in condition checking) did the reverse of it. i.e if the div has a container obj, it was converted to paragraph. This has been fixed. This fix incidentally increases the processing time. The interesting part of this is that, this reversal gives better result than actual arc90 readability. Case in point, for Nature – Wikipedia, the See Also section would be removed by arc90 but is neatly retained by python-readability. Adding this fix again causes irregularity in See Als section. The below fix takes care of that.</li>
<li>Once the main section of meaningful text has been determined, a function iterates through all the descendants of the main section and prunes unnecessary crufts. One of such logic is to remove lists whose link density is &#62; 0.2. This also prunes away valid links to relevant content. The main purpose of this logic is to remove any navigational links. So I’ve added an additional constraint for pruning that the length content should be less than 300, to avoid pruning meaningful content.</li>
<li>some images embedded in text are lost because of the pruning logic (e.g the first img in Wikipedia Nature, <a href="http://en.wikipedia.org/wiki/Hopetoun_Falls" title="Hopetoun Falls">Hopetoun Falls</a>, <a href="http://en.wikipedia.org/wiki/Australia" title="Australia">Australia</a>, is lost after applying arc90′s readability). Added extra logic to retain images based on their img size.</li>
</ol>
<h3>Installation</h3>
<p>Currently the installation option is to <a href="http://code.google.com/p/decruft/downloads/list">download</a> it from google<br />
code.</p>
<pre><code class="python">    tar –zxf decruft&lt;em&gt;-x.y&lt;/em&gt;.tgz
    export PYTHONPATH=$PYTHONPATH:$PWD/decruft
    python

</code></pre>
<pre><code class="python">    &gt;&gt;&gt; from decruft import Document
    &gt;&gt;&gt; import urllib2
    &gt;&gt;&gt; f = urllib2.open(&lt;em&gt;url&lt;/em&gt;)
    &gt;&gt;&gt; print Document(f.read()).summary()

</code></pre>
<p>I’m looking forward to writing a pip/easy&#95;install installer for the same.</p>
