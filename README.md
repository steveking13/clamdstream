# clamdstream
Lightweight streaming client for clamd

clamd provides a TCP interface for scanning files. The clamdscan executable profided as part of the clamav suite can use this, but only via localhost.

As clamd is memory heavy, it would be nice to be able to use this remote capability in environments where memory is at a premium,
for example Raspberry Pi or virtual machines.

This is a fairly simple python script that will recursively traverse a directory and pass the content of the files it finds to a host:port where a
clamd instance is running.

# Configuration notes
### clamd

The server that is running clamd.

You will need a line in /etc/clamav/clamd.conf:<br>
<pre>TCPSocket 3310</pre><br>
(3310 is the default socket)<br>
If you have a multi-homed server, you may want the line:<br>
<pre>TCPAddr &lt;address&gt;</pre><br>
Where address is a local interface. This will restrict which addresses the host listens on.
  
Clamav recommend against exposing this to untrusted networks.

### amavis

The client that is wanting to virus scan some stuff.

If using with amavis, you will need a stanza in your /etc/amavis/conf.d/15-av_scanners file:
  
<pre># This is a remote instance, nINSTREAM should pass the
# suspect file over the TCP connection
 ['ClamAV-remote-stream',
  '/usr/local/bin/clamdstream',
  "&lt;hostname or IP&gt; {}" ,
   qr/\bOK$/m, qr/\bFOUND$/m,
   qr/^.*?: (?!Infected Archive)(.*) FOUND$/m ],</pre>

where &lt;hostname or IP&gt; is replaced with the identity of the computer on which clamd is running.
