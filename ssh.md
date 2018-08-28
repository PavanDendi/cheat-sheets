### Use "master" mode to control connections that have been sent to background
```
ssh -f -N -M -S <path-to-socket> -L <local-port>:<remote-host>:<remote-port> <server>
```
`-f`&emsp;send to background\
`-N`&emsp;do not run a command on the remote server\
`-M`&emsp;"master" mode\
`-S`&emsp;path to socket control file.  It is recommended that any ControlPath used for opportunistic connection sharing include at least %h, %p, and %r.  Following substitutions are valid:
* `%L`&emsp;first component of the local host name
* `%l`&emsp;local host name (including any domain name)
* `%h`&emsp;target host name
* `%n`&emsp;original target host name specified on the command line
* `%p`&emsp;destination port
* `%r`&emsp;remote login username
* `%u`&emsp;username of the user running ssh(1)
* example: `~/.ssh/%r@%h:%p`

`-L`&emsp;forward local port to a remote port
* `<local-port>`&emsp;port on local machine
* `<remote-host>`&emsp;name of remote host relative to server
* `<remote-port>`&emsp;port on remote host

`<server>`&emsp;name of bastion server that will be doing the port forwarding

### Close the connection

```
ssh -S <path-to-socket> -O exit <server>
```
`-S <path-to-socket>`&emsp;path to socket control file, same as when used to create the tunnel\
`-O exit`&emsp; control the socket.  `exit` will close the connection.  See `man ssh` for other commands