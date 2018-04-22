# scp, tar, rsync

Instead of using tar to write to your local disk, you can write directly to the remote server over the network using ssh.

server1$ tar -zc ./path | ssh server2 "cat > ~/file.tar.gz"
Any string that follows your "ssh" command will be run on the remote server instead of the interactive logon. You can pipe input/output to and from those remote commands through SSH as if they were local. Putting the command in quotes avoids any confusion, especially when using redirection.

Or, you can extract the tar file on the other server directly:

server1$ tar -zc ./path | ssh server2 "tar -zx -C /destination"
Note the seldom-used -C option. It means "change to this directory first before doing anything."

Or, perhaps you want to "pull" from the destination server:

server2$ tar -zx -C /destination < <(ssh server2 "tar -zc -C /srcdir ./path")
Note that the <(cmd) construct is new to bash and doesn't work on older systems. It runs a program and sends the output to a pipe, and substitutes that pipe into the command as if it was a file.

I could just have easily have written the above as follows:

server2$ tar -zx -C /destination -f <(ssh server2 "tar -zc -C /srcdir ./path")
Or as follows:

server2$ ssh server2 "tar -zc -C /srcdir ./path" | tar -zx -C /destination
Or, you can save yourself some grief and just use rsync:

server1$ rsync -az ./path server2:/destination/
Finally, remember that compressing the data before transfer will reduce your bandwidth, but on a very fast connection, it may actually make the operation take more time. This is because your computer may not be able to compress fast enough to keep up: if compressing 100MB takes longer than it would take to send 100MB, then it's faster to send it uncompressed.

Alternately, you may want to consider piping to gzip yourself (rather than using the -z option) so that you can specify a compression level. It's been my experience that on fast network connections with compressible data, using gzip at level 2 or 3 (the default is 6) gives the best overall throughput in most cases. Like so:

server1$ tar -c ./path | gzip -2 | ssh server2 "cat > ~/file.tar.gz"
