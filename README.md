# clamdstream
Lightweight streaming client for clamd

clamd provides a TCP interface for scanning files. The clamdscan executable profided as part of the clamav suite can use this, but only via localhost.

As clamd is memory heavy, it would be nice to be able to use this remote capability in enfironments where memory is at a premium,
for example Raspberry Pi or virtual machines.

This is a fairly simple python script that will recursively traverse a directory and pass the content of the files it finds to a host:port where a
clamd instance is running.

# Configuration notes
clamd.conf

You will need a line:
TCPSocket 3310
(3310 is the default socket)
You may want the line:
TCPAddr <address>
to restrict which addresses the host listens on.
  
Clamav recommend against exposing this to untrusted networks.

amavis

If using with amavis, you will need a stanza in your 15-av_scanners file:
# This is a remote instance, nINSTREAM should pass the
# suspect file over the TCP connection
 ['ClamAV-remote-stream',
  '/usr/local/bin/clamdstream',
  "<hostname or IP> {}" ,
   qr/\bOK$/m, qr/\bFOUND$/m,
   qr/^.*?: (?!Infected Archive)(.*) FOUND$/m ],

where <hostname or IP> is replaced with the identity of the computer on which clamd is running.
