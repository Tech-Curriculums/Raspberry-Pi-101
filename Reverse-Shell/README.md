Reverse Shell
=============


Sometimes we want to do things on a network we aren't physically at. :trollface:

Here we will look at how to plant a remote access brick in any location using the raspberry pi.

## Steps

* setting up ssh
* creating reverse ssh tunnel
* deciding when the tunnel should be on (github switch, xor always)
* Encasing your pi

## Projects:

* PiCam takes a photo and emailing it to you (did you leave the garage door open?)
* Networked PiCams all do the same and you can view every part of the house.
* turn on the lights
* say something to the people who are home (espeak)
* ssh into other local computers and play pranks

---

## Setting Up SSH

(3 steps, 2 minutes)

1. create a public/private keypair for your raspberry pi
```sh
ssh-keygen -r rsa
```

2. Leave passphrase empty, and allow the default configurations for name and location (~/.ssh)

3. copy the PUBLIC key (`id_rsa.pub`) and copy it back to your personal computer -- never share the private key (which is the `id_rsa` file -- note no `.pub` at the end)

Below is how I would go about it (I have a habit of checking what's there with `ls` as a sanity check)

```sh
cd ~/.ssh
ls
cp id_rsa.pub yourusername@youripaddress:~/.ssh/authorized_keys
```

**Replace `yourusername` with your laptop's username and `youripaddress` with your ip address found by typing `ifconfig`**

## Reverse the Shell

(2 steps, 3 minutes)

#### Now that your raspberry pi and your computer are sharing the keys, simply have the pi bring it's tunnel to your lappy:

```sh
ssh -N -R 2222:localhost:22 serverusername@serveripaddress
```

**Replace `serverusername` with your server's username and `serveripaddress` with the ip address of server**

===

#### Next we'll make a script to automate this:

```
#!/bin/bash

# see if there's a process called ssh running
/bin/pidof ssh

# the special symbol $? gives success of last command (in this case pidof ssh):
#    you should in general expect $? to return `0` for a success and `1` for an ERROR 
if [[ $? -ne 0 ]]; then
  /usr/bin/ssh -N -R 2222:localhost:22 serverusername@serveripaddress
fi
```
**Remember to replace `serverusername` and `serveripaddress` as reviewed before**

===

#### finally we'll set it up to check (fixing if necessary) every 1 minute:

```sh
chmod 700 create_the_tunnel.sh
crontab -e
```
inside crontab do the following:

```sh
* * * * * ~/create_the_tunnel.sh
```


## References

http://www.tunnelsup.com/raspberry-pi-phoning-home-using-a-reverse-remote-ssh-tunnel
