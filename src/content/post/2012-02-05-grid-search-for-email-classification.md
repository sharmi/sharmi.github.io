---
 
title: Grid Search for Email Classification
publishDate: 2012-02-05 12:42:00.000000000 +05:30

category:  Machine Learning
tags:
- natural language processing
- python
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2899417577'
author:  sharmi
 
canonical: "https://www.minvolai.com/grid-search-for-email-classification/"
slug: grid-search-for-email-classification
---
<p>As continued from the <a href="http://localhost:10003/blog/2012/01/email-classification-supervised-learning/">Supervised Classification</a> Post, we have been running grid search on the email classification algorithm varying Gamma and C.  The result are as below. Each column represents a Gamma value and each row represents a C value. The value of the internal grids themselves are error rate percentage for the given value of Gamma and C.</p>
<p>The eye-opening observation of this experiment is how much Gamma influences the error rate compared to C.  While any value of C less than 1 is bad, probably due to over fitting, the performance of the Classification Algorithm does not vary much where C >= 1.  Whereas, distinct gamma values have a more concrete effect on the error value of the Classifcation.</p>
<p>Test data:</p>
<p>I took the parameters pertaining to the lowest error value (3.75) resulting in gamma value at 0.03125 and C value at 4.  I use these values to evaluate the algorithm on the test data. The resultant Error rate is 4.6%</p>
<table width="100%" frame="BELOW" rules="ROWS" cellspacing="1" cellpadding="0">
<colgroup>
<col width="43*" /> </colgroup>
<colgroup>
<col width="43*" />
<col width="43*" />
<col width="43*" />
<col width="43*" /> </colgroup>
<colgroup>
<col width="43*" /> </colgroup>
<thead>
<tr valign="TOP">
<th width="17%">
<p align="LEFT">Gamma<br />
-&gt;</p>
<p align="LEFT">C</p>
</th>
<th width="17%">
<p align="LEFT">0.0009765625</p>
</th>
<th width="17%">
<p align="LEFT">0.03125</p>
</th>
<th width="17%">
<p align="LEFT">0.25</p>
</th>
<th width="17%">
<p align="LEFT">1</p>
</th>
<th width="17%">
<p align="LEFT">4</p>
</th>
</tr>
</thead>
<tbody>
<tr valign="TOP">
<td width="17%">
<p align="LEFT">0.03125</p>
</td>
<td width="17%">
<p align="LEFT"><span>18.15</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>9.75</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>44.15</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>46.2</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>46.4</span></p>
</td>
</tr>
<tr valign="TOP">
<td width="17%">
<p align="LEFT">0.25</p>
</td>
<td width="17%">
<p align="LEFT"><span>7.25</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>5.4</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>18.05</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>14.2</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>38.15</span></p>
</td>
</tr>
<tr valign="TOP">
<td width="17%">
<p align="LEFT">1</p>
</td>
<td width="17%">
<p align="LEFT"><span>5.65</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>4.15</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>11</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>14.85</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>17</span></p>
</td>
</tr>
<tr valign="TOP">
<td width="17%">
<p align="LEFT">4</p>
</td>
<td width="17%">
<p align="LEFT"><span>4.6</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>3.75</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>10.95</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>14.55</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>17</span></p>
</td>
</tr>
<tr valign="TOP">
<td width="17%">
<p align="LEFT">32</p>
</td>
<td width="17%">
<p align="LEFT"><span>3.95</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>3.95</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>10.95</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>14.55</span></p>
</td>
<td width="17%">
<p align="LEFT"><span>17</span></p>
</td>
</tr>
<tr valign="TOP">
<td width="17%">
<p align="LEFT"><span>1024</span></p>
</td>
<td width="17%">
<p align="LEFT">3.9</p>
</td>
<td width="17%">
<p align="LEFT">4.15</p>
</td>
<td width="17%">
<p align="LEFT">10.95</p>
</td>
<td width="17%">
<p align="LEFT">14.55</p>
</td>
<td width="17%">
<p align="LEFT"><span>17</span></p>
</td>
</tr>
</tbody>
</table>
