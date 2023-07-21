---
 
title: 'Chardet: Detecting Unknown String Encodings'
publishDate: 2009-11-14 22:53:54.000000000 +05:30

category:  Python External Library
tags:
- chardet
- encoding
- python
- python external library
- text processing
- unicode
- utf8
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2954616259'
author:  sharmi
 
canonical: "https://www.minvolai.com/chardet-detecting-unknown-string-encodings/"
slug: chardet-detecting-unknown-string-encodings
---
<p><a href="http://images.minvolai.com/albums/minvolai/babel.jpg"><img src="{{ site.baseurl }}/assets/2009/11/babel.jpg" alt="" /></a></p>
<p>Have you ever worked with data extracted from a random source?  Like an unknown website?  This can sometimes become a nightmare for developpers as it is impossible to determine the encoding. Further text processing  without using the correct encoding can become error prone.  Lets see how to handle the different situations where encoding is known or unknown.</p>
<p><!--more--></p>
<p>Let's look at Python's default datatypes for handling text strings,  'byte string' and 'unicode'. Both are of the type category sequence.</p>
<p>In 'byte string' format the text is represented as sequence of bytes in one of the supported encoding (utf-n, multibyte encodings, singlebyte encodings) the default being ascii). The unicode string representation is a sequence of either 16 or 32 bit integers depending on whether python is compiled with --enable-unicode=ucs2 or --enable-unicode=ucs4</p>
<p>The default encoding for byte string is 'ascii'.  To check,</p>
<pre><code class="python"><br />    &gt;&gt;&gt; import sys

    &gt;&gt;&gt; sys.getdefaultencoding()

    'ascii'

</code></pre>
<h2>Uniform Handling of Strings of known Encodings</h2>
<p>The general idea of handling strings of different encodings, when the encodings are known is to convert them to a common format.  When a byte string is decoded, the resulting string is an python unicode string.  When the python unicode string is encoded, the result is a byte string.  Thus the texts can be decoded from their corresponding encodings into the python unicode string.  Once the text is processed, they can be converted back to their respective formats.</p>
<pre><code class="python"><br />    &gt;&gt;&gt; x = "புதுச் சொற்களை உருவாக்க இந்தப் பக்கத்துக்குச் செல்லவு"
    &gt;&gt;&gt; type(x)

    &gt;&gt;&gt; x
    '\xe0\xae\xaa\xe0\xaf\x81\xe0\xae\xa4\xe0\xaf\x81\xe0\xae\x9a\xe0\xaf\x8d \xe0\xae\x9a\xe0\xaf\x8a\xe0\xae\xb1\xe0\xaf\x8d\xe0\xae\x95\xe0\xae\xb3\xe0\xaf\x88 \xe0\xae\x89\xe0\xae\xb0\xe0\xaf\x81\xe0\xae\xb5\xe0\xae\xbe\xe0\xae\x95\xe0\xaf\x8d\xe0\xae\x95 \xe0\xae\x87\xe0\xae\xa8\xe0\xaf\x8d\xe0\xae\xa4\xe0\xae\xaa\xe0\xaf\x8d \xe0\xae\xaa\xe0\xae\x95\xe0\xaf\x8d\xe0\xae\x95\xe0\xae\xa4\xe0\xaf\x8d\xe0\xae\xa4\xe0\xaf\x81\xe0\xae\x95\xe0\xaf\x8d\xe0\xae\x95\xe0\xaf\x81\xe0\xae\x9a\xe0\xaf\x8d \xe0\xae\x9a\xe0\xaf\x86\xe0\xae\xb2\xe0\xaf\x8d\xe0\xae\xb2\xe0\xae\xb5\xe0\xaf\x81'
    &gt;&gt;&gt; print x
    à®ªà¯à®¤à¯à®šà¯ à®šà¯Šà®±à¯à®•à®³à¯ˆ à®‰à®°à¯à®µà®¾à®•à¯à®• à®‡à®¨à¯à®¤à®ªà¯ à®ªà®•à¯à®•à®¤à¯à®¤à¯à®•à¯à®•à¯à®šà¯ à®šà¯†à®²à¯à®²à®µà¯
    &gt;&gt;&gt; y = x.decode('utf8')
    &gt;&gt;&gt; y
    u'\u0baa\u0bc1\u0ba4\u0bc1\u0b9a\u0bcd \u0b9a\u0bca\u0bb1\u0bcd\u0b95\u0bb3\u0bc8 \u0b89\u0bb0\u0bc1\u0bb5\u0bbe\u0b95\u0bcd\u0b95 \u0b87\u0ba8\u0bcd\u0ba4\u0baa\u0bcd \u0baa\u0b95\u0bcd\u0b95\u0ba4\u0bcd\u0ba4\u0bc1\u0b95\u0bcd\u0b95\u0bc1\u0b9a\u0bcd \u0b9a\u0bc6\u0bb2\u0bcd\u0bb2\u0bb5\u0bc1'
    &gt;&gt;&gt; print type(y)

    &gt;&gt;&gt; print y
    புதுச் சொற்களை உருவாக்க இந்தப் பக்கத்துக்குச் செல்லவு
    &gt;&gt;&gt; print len(x)
    149
    &gt;&gt;&gt; print len(y)
    53
    &gt;&gt;&gt; #Process y
    &gt;&gt;&gt; x2 = y.encode('utf8')
    &gt;&gt;&gt; type(x2)

    &gt;&gt;&gt; x == x2
    True


</code></pre>
<p>When the value is assigned to x, the value is considered a string of bytes.  When x is printed, the byte string is interpreted as ascii characters  as that is the default encoding.  The resultant output is gibberish.  When x is decoded using the utf8 codec (which is the actual encoding of the text), the resulting unicode string inteprets the characters correctly as can be seen when y is printed.  There is a mismatch between the length of x and y because, in x the utf8 tamil characters do not fit into the values of 0-128.  Hence they are represented using multibyte values in the range of 128-255.  Refer Python's Unicode support : http://www.amk.ca/python/howto/unicode</p>
<p>UTF-8 is one of the most commonly used encodings. UTF stands for "Unicode Transformation Format", and the '8' means that 8-bit numbers are used in the encoding. (There's also a UTF-16 encoding, but it's less frequently used than UTF-8.) UTF-8 uses the following rules:</p>
<ol>
<li>If the code point is &lt;128, it's represented by the corresponding byte value.</li>
<li>If the code point is between 128 and 0x7ff, it's turned into two byte values between 128 and 255.    </li>
<li>Code points >0x7ff are turned into three- or four-byte sequences, where each byte of the sequence is between 128 and 255.</li>
</ol>
<pre><code class="python"><br /><br />    &gt;&gt;&gt; x =  "äöü"
    &gt;&gt;&gt; x
    '\xc3\xa4\xc3\xb6\xc3\xbc'
    &gt;&gt;&gt; type (x)

    &gt;&gt;&gt; y = x.decode('utf8')

    u'\xe4\xf6\xfc'
    &gt;&gt;&gt; type(x.decode('utf8'))

    &gt;&gt;&gt; type(x.decode('utf8').encode('utf8'))



</code></pre>
<p>Once the string is processed, it can be converted to its original encoding.</p>
<h2>Handling files with known encodings</h2>
<p>The builtin file open function reads the file using ascii encoding.  This would result in undesireable character interpretations.  Python provides the codecs module to handle non-ascii encoded files.</p>
<pre><code class="python"><br /><br />    &gt;&gt;&gt; import codecs
    &gt;&gt;&gt; data = codecs.open(r"E:\sharmi\our website\blogging\utf8_sample.txt", 'r', 'utf8').read(100)
    &gt;&gt;&gt; data
    u'\ufeff    * \u0baa\u0bc1\u0ba4\u0bc1\u0b9a\u0bcd \u0b9a\u0bca\u0bb1\u0bcd\u0b95\u0bb3\u0bc8 \u0b89\u0bb0\u0bc1\u0bb5\u0bbe\u0b95\u0bcd\u0b95 \u0b87\u0ba8\u0bcd\u0ba4\u0baa\u0bcd \u0baa\u0b95\u0bcd\u0b95\u0ba4\u0bcd\u0ba4\u0bc1\u0b95\u0bcd\u0b95\u0bc1\u0b9a\u0bcd \u0b9a\u0bc6\u0bb2\u0bcd\u0bb2\u0bb5\u0bc1\u0bae\u0bcd.\r\n    * \u0bb5\u0bbf\u0b95\u0bcd\u0b9a\u0ba9\u0bb0\u0bbf \u0baa\u0bb1\u0bcd\u0bb1\u0bbf\u0baf \u0b95\u0bb2\u0ba8\u0bcd\u0ba4\u0bc1\u0bb0\u0bc8\u0baf\u0bbe\u0b9f\u0bb2\u0bcd\u0b95\u0bb3\u0bc8 \u0b86\u0bb2\u0bae\u0bb0\u0ba4\u0bcd\u0ba4\u0b9f\u0bbf\u0baf\u0bbf'
    &gt;&gt;&gt; print data
    ﻿    * புதுச் சொற்களை உருவாக்க இந்தப் பக்கத்துக்குச் செல்லவும்.
        * விக்சனரி பற்றிய கலந்துரையாடல்களை ஆலமரத்த
    &gt;&gt;&gt; print type(data)


    &gt;&gt;&gt; codecs.open('output_file', 'w', 'utf8').write(data)


</code></pre>
<p>Here the conversion to unicode is done implicitly by the codecs module.  Conversion back to utf8 is also done implicitly when the codecs's file handler.</p>
<h2>Handling Byte Strings of Unknown Encodings: Chardet</h2>
<p>Chardet (http://chardet.feedparser.org) is a python library that systematically detects character encodings of the text fed to it.  It is a port of Mozilla's auto-detection code 'universal chardet' http://www.mozilla.org/projects/intl/chardet.html.  As a sample, we shall download a webpage from a tamil dictionary site and from google search's chinese and feed them to chardet.  We can see that it gives both the encoding and the confidence level on a scale of 0 to 1 as a dictionary.</p>
<pre><code class="python"><br /><br />    &gt;&gt;&gt; import chardet

    &gt;&gt;&gt; import urllib2

    &gt;&gt;&gt; ta_dic = urllib2.urlopen("http://www.tamildict.com/english.php").read()

    &gt;&gt;&gt; chardet.detect(ta_dic)

    {'confidence': 0.98999999999999999, 'encoding': 'utf-8'}

    &gt;&gt;&gt; g_cn = urllib2.urlopen("http://www.google.cn/").read()

    &gt;&gt;&gt; chardet.detect(g_cn)

    {'confidence': 0.98999999999999999, 'encoding': 'GB2312'}

</code></pre>
<p>chardet does a sequence of probing to determine if it is a UTF-n with a BOM (Byte Order Marking), Escaped Encoding, multibyte encoding or windows-1252.  This process can become time consuming for a huge chunk of text.  In such cases, we can feed chardet just enough text until it can guess the encoding with reasonable accuracy.  An instance of the UniversalDetector class is created and the data is feed to it incrementally.  The detector's done flag is set once it reaches the minimum confidence level.  If the text has been completely fed and a result has not been reached, calling detector's close function will do further computation and provide a reasonable encoding result.</p>
<pre><code class="python"><br />    &gt;&gt;&gt; from chardet.universaldetector import UniversalDetector 

    &gt;&gt;&gt; sample_f = open(r"E:\sharmi\our website\blogging\utf8_sample.txt", 'r')

    &gt;&gt;&gt; detector = UniversalDetector()

    &gt;&gt;&gt; for line in sample_f:

    ...     detector.feed(line)

    ...     if detector.done: 

    ...         break;

    ...     

    &gt;&gt;&gt; detector.close()

    &gt;&gt;&gt; print detector.result

    {'confidence': 1.0, 'encoding': 'UTF-8'}

</code></pre>
<h2>Chardet: Installation</h2>
<p>It can be downloaded and installed from their installation page http://chardet.feedparser.org/download/</p>
<p>On debian machines it can be installed using sudo easy_install chardet</p>
<p>sudo apt-get install python-chardet</p>
<h2>Further Reading</h2>
<p><a href="http://chardet.feedparser.org/docs/how-it-works.html">Chardet: Universal Encoding Detector: How it Works </a></p>
<p><a href="http://www.amk.ca/python/howto/unicode">Python's Unicode support </a></p>
<p><a href="http://www.joelonsoftware.com/articles/Unicode.html">The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)</a></p>
<p>by Joel Spolsky</p>
<p><a href="http://evanjones.ca/python-utf8.html">How to Use UTF-8 with Python </a></p>
<p><a href="http://docs.python.org/library/codecs.html">Python Standard Library: codecs</a></p>
<p><a href="http://boodebr.org/main/python/all-about-python-and-unicode">All About Python and Unicode </a></p>
