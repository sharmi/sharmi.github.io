---
 
title: 'Fix for VBoxManage: error: Could not find a controller named ''SATA'' Error'
pubDate: 2018-10-04 18:36:59.000000000 +05:30

category:  Uncategorised
tags: []
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _yoast_wpseo_content_score: '90'
  _yoast_wpseo_primary_category: ''
  _wpas_done_all: '1'
author:  sharmi
 
canonical: "https://www.minvolai.com/fix-for-vboxmanage-error-could-not-find-a-controller-named-sata-error/"
slug: fix-for-vboxmanage-error-could-not-find-a-controller-named-sata-error
description: "Fix for the error 'VBoxManage: error: Could not find a controller named 'SATA' when trying to reattach a resized hard disk to a virtual machine."
---
<p>I was using this guide <a href="https://gist.github.com/miraleung/5fe18f7d68994024862a">Resize a Hard Disk for a Virtual Machine</a> to resize the hard disk for my vagrant machine (provisioned through virtual box). It is an ubuntu 18.04 image. I successfully managed to make a vdi clone and resize it.</p>
<p>When trying to reattach it with the following command</p>
<pre><code>VBoxManage storageattach sitefit_default_1537859062545_25454 --storagectl "SATA" --port 0 --device 0 --type hdd --medium clone-disk1.vdi
</code></pre>
<p>I ran into the error</p>
<pre><code>VBoxManage: error: Could not find a controller named 'SATA'
</code></pre>
<p>I tried googling around. While it did not give a direct solution, I realized that I should change the storagectl parameter. Paying a little closer attention to the result of the <code>showvminfo</code> command solved the issue.</p>
<pre><code>~/V/sitefit_default_1537859062545_25454&gt; VBoxManage showvminfo sitefit_default_1537859062545_25454 | grep "Storage"
Storage Controller Name (0):            IDE Controller
Storage Controller Type (0):            PIIX4
Storage Controller Instance Number (0): 0
Storage Controller Max Port Count (0):  2
Storage Controller Port Count (0):      2
Storage Controller Bootable (0):        on
Storage Controller Name (1):            SCSI Controller
Storage Controller Type (1):            LsiLogic
Storage Controller Instance Number (1): 0
Storage Controller Max Port Count (1):  16
Storage Controller Port Count (1):      16
Storage Controller Bootable (1):        on
</code></pre>
<p><code>--storagectl</code> needs the storage control name. In the result above, the <code>Storage Controller Name</code> is set to <code>IDE Controller</code>. Making the same substitution in the above command results in success.</p>
<pre><code>VBoxManage storageattach sitefit_default_1537859062545_25454 --storagectl "IDE Controller" --port 0 --device 0 --type hdd --medium clone-disk1.vdi
</code></pre>
