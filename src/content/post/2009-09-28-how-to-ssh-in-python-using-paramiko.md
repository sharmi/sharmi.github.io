---
title: How to ssh in python using Paramiko?
publishDate: 2009-09-28 13:03:01.000000000 +05:30
category: python
tags: []
author: Sharmi
canonical: "https://www.minvolai.com/how-to-ssh-in-python-using-paramiko/"
slug: how-to-ssh-in-python-using-paramiko
---
<p>If you have ever agonized over connecting and communicating with a remote machine in python, give Paramiko a go.  Paramiko is most helpful for cases where one needs to securely communicate and exchange data,  execute commands on remote machines, handle connect requests from remove machines or access ssh services like sftp. As described in the <a href="http://www.lag.net/paramiko/">paramiko's homepage</a></p>
<blockquote><p>
  "Paramiko is a module for python 2.2 (or higher) that implements the SSH2 protocol for secure (encrypted and authenticated) connections to remote machines."
</p></blockquote>
<p><!--more--></p>
<h3>Paramiko: Dependencies &amp; Installation</h3>
<p>The version I'm using is 1.7.5.  paramiko is written purely in python and the only dependency for it is pycrypto 1.9+.<br />
I installed using easy_install</p>
<pre><code class="python"><br />    sudo easy_install paramiko

</code></pre>
<p>On ubuntu, paramiko can be installed by</p>
<pre><code class="python"><br />    sudo apt-get paramiko

</code></pre>
<p>The rpm equivalent is also available.</p>
<p>Another option is to download the source code/module from the parent site and install using '</p>
<pre><code class="python"><br />    wget http://www.lag.net/paramiko/download/paramiko-1.7.5.zip
    unzip paramiko-1.7.5.zip
    cd paramiko-1.7.5
    python setup.py install

</code></pre>
<p>To use paramiko effectively, We will need to understand the basics of ssh.</p>
<h2>SSH and Security</h2>
<p>SSH, or Secure SHell, as a network protocol provides mechanism for authenticated and encrypted connections between remote machines over unsecure network.Â  Before ssh was employed, telnet and rcp protocols exchanged authentication information as plaintext over unsecure network, while establishing the connection.Â Â  SSH solved this by using encrypted communication even while establishing the connection, effectively replacing other network protocols.</p>
<p>SSH provides</p>
<ul>
<li>Encryption of all communication between the two entities with a several cipher algorithms to choose from</li>
<li>Authentication using password or a public key or using both options as a two-factor authentication</li>
<li>Ensuing integrity of data is by creating a digital signature of the data transferred from one entity to another.Â  There are multiple message authentication algorithmsÂ  to choose from the signature creation.</li>
</ul>
<h2>Connecting Using Paramiko</h2>
<p>Paramiko supports both password based authentication and public key based authentication. Paramiko's API facilitate both high level and low level control over the ssh connection.</p>
<p>The simplest and probably the easiest way to connect to a remote machine using Paramiko is the SSHClient object.Â  It is a high-level representation of a session with an SSH server. This class incorporates Transport, Channel, and SFTPClient to provide simpler authentication and connection apis.</p>
<p>The ssh server will be verified by the host keys loaded from the user's local ssh's known_hosts file.Â  In case of failure to verify, the default policy is to reject the server's keys and raise an SSHException.Â  Here I'm overriding it with the AutoAddPolicy wherein the new server will be automatically added to the list of known hosts. Its also possible to define our own policies on how to handle unidentified servers.</p>
<p>Authentication of the client is attempted in the following order of priority:</p>
<ul>
<li>The pkey or key_filename passed in (if any)</li>
<li>Any key we can find through an SSH agent</li>
<li>Any "id_rsa" or "id_dsa" key discoverable in ~/.ssh/</li>
<li>Plain username/password auth, if a password was given</li>
</ul>
<h3>Using Paramiko's SSHClient</h3>
<p>In the below example I'm using the password to authenticate.</p>
<pre><code class="python"><br />    import os
    import paramiko

    server, username, password = ('host', 'username', 'password')
    ssh = paramiko.SSHClient()
    parmiko.util.log_to_file(log_filename)
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    #In case the server's key is unknown,
    #we will be adding it automatically to the list of known hosts
    ssh.load_host_keys(os.path.expanduser(os.path.join("~", ".ssh", "known_hosts")))
    #Loads the user's local known host file.
    ssh.connect(server, username=username, password=password)
    ssh_stdin, ssh_stdout, ssh_stderr = ssh.exec_command('ls /tmp')
    print "output", ssh_stdout.read() #Reading output of the executed command
    error = ssh_stderr.read()
    #Reading the error stream of the executed command
    print "err", error, len(error) 

    #Transfering files to and from the remote machine
    sftp = ssh.open_sftp()
    sftp.get(remote_path, local_path)
    sftp.put(local_path, remote_path)
    sftp.close()
    ssh.close()

</code></pre>
<p>The 'connect' method also allows you to provide your own private key, or connect to the SSH agent on the local machine or read from the user's local key files. Click here for a more detailed description ofÂ  the '<a href="http://www.lag.net/paramiko/docs/paramiko.SSHClient-class.html#connect">connect</a>' method's signature.</p>
<pre><code class="python"><br />    privkey = paramiko.RSAKey.from_private_key_file (path_to_priv_key_file)
    ssh.connect(server, username=username,pkey=privkey )
    #private key to use for authentication

</code></pre>
<p>or</p>
<pre><code class="python"><br />    ssh.connect(server, username=username, key_filename=path_to_priv_key_file)
    #the filename, or list of filenames, of optional private key(s)
    #to try for authentication

</code></pre>
<p>or</p>
<pre><code class="python"><br />    ssh.connect(server, username=username, allow_agent=True)
    #Connects to the local SSH agent and tries to obtain the private key

</code></pre>
<p>or</p>
<pre><code class="python"><br />    ssh.connect(server, username=username, look_for_keys=True)
    #Searches for discoverable private key files in ~/.ssh/

</code></pre>
<h3>The Transport Object</h3>
<p>It provides more direct control over how the connections are formed and authentication is carried over.Â  It provides for variousÂ  options such as logging debugging information like hex dump, manipulating the algorithms used and their priorities, controlling the authentication methods and their sequence, the type of channels to open, forcing renegotiating keys etc.Â  It is also possible to start an SSH Server or SFTP Server using this object.</p>
<p>The below example uses the Transport Object to connect:</p>
<p>import os<br />
import paramiko<br />
server, port, username, password = ('host', 22, 'username', 'password')<br />
parmiko.util.log_to_file(log_filename)<br />
nbytes = 100</p>
<blockquote><p>
  An SSH Transport attaches to a stream (usually a socket), negotiates an encrypted session, authenticates, and then creates stream tunnels, called Channels, across the session. Multiple channels can be multiplexed across a single session (and often are, in the case of port forwardings).
</p></blockquote>
<pre><code class="python"><br />    trans = paramiko.Transport((host, port))
    trans.connect(username = username, password = password)

</code></pre>
<p>Next, after authentication, we create a channel of type "session"</p>
<pre><code class="python"><br />    session = trans.open_channel("session")
    #Once the channel is established, we can execute only one command.Â  To execute another command, we need to create another channel
    session.exec_command('ls /tmp')

</code></pre>
<p>We need to wait till the command is executed or the channel is closed.Â  recv_exit_status  return the result of the exit status of the command executed or -1 if no exit status was given.</p>
<pre><code class="python"><br />    exit_status = session.recv_exit_status()

</code></pre>
<p>Now the command execution is over and stdout and stderr streams will be linked to the channel and can be read.</p>
<pre><code class="python"><br />    stdout_data = []
    stderr_data = []

    while session.recv_ready():
    stdout_data.append(session.recv(nbytes))
    stdout_data = "".join(stdout_data)

    while session.recv_stderr_ready():
    stderr_data.append(session.recv_stderr(nbytes))
    stderr_data = "".join(stderr_data)

    print "exit status", exit_status
    print "output"
    print stdout_data
    print "error"
    print stderr_data

</code></pre>
<p>We can also create SFTP client from the Transport Object as below</p>
<pre><code class="python"><br />    sftp = paramiko.SFTPClient.from_transport(trans)
    sftp.get('remote_path', 'local_path')
    sftp.put('local_path', 'remote_path')
    sftp.close()

</code></pre>
<p>SFTP client object. SFTPClient is used to open an sftp session across an open ssh Transport and do remote file operations.</p>
<p>Personally I prefer to write a wrapper module over the SSHClient api and use that in my day to day needs.</p>
<h2>Caveats</h2>
<p>Paramiko's SFTPClient is significantly slower compared to sftp or scp, some times by an order of magnitude, especially for huge files.Â  The scp implementation listed in the 'Interesting Read' below assures to be faster though I've not tested it yet.</p>
<p>Paramiko's SSHClient does not allow for setting a timeout for exec_command.Â  This means that, in case of the remote_machine not returning the exec_command call, the process would freeze and need to be killed.</p>
<h2>Interesting Read</h2>
<p><a href="http://jessenoller.com/2009/02/05/ssh-programming-with-paramiko-completely-different/">SSH Programming with Paramiko | Completely Different</a> <a href="http://code.activestate.com/recipes/576810/">Recipe 576810: Copy files over SSH using paramiko</a> <a href="http://www.google.co.in/url?sa=t&amp;source=web&amp;ct=res&amp;cd=1&amp;url=http%3A%2F%2Fwww.lag.net%2Fparamiko%2F&amp;ei=zNDBSsTuLsOslAffssHIBQ&amp;usg=AFQjCNEwiYP2TK6otNcF5Dbwhf36llUPNQ&amp;sig2=N7e41lX0hwnRG_T29KpB2w">Paramiko Home</a> <a href="http://www.lag.net/pipermail/paramiko/2008-February/000614.html">Implementing SCP in Paramiko</a></p>
