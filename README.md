# ipfs-cheatsheet
List of useful commands when configuring and working with IPFS


## install IPFS
```
wget https://dist.ipfs.io/go-ipfs/v0.4.20/go-ipfs_v0.4.20_linux-amd64.tar.gz
tar zxvf go-ipfs_v0.4.20_linux-amd64.tar.gz
cd go-ipfs && ./install.sh
```

install tool to pretty print json and file tree
```
sudo apt-get -y update && sudo apt-get -y install jq tree
```

init ipfs storage
```
ipfs init
```

increase opened files limit
```
ulimit -n 5560
```
(optional) Configure API and Status page to listen on public interface

Note! Publishing API is unsecure because anyone can use it
```
ipfs config Addresses.Gateway /ip4/0.0.0.0/tcp/80
ipfs config Addresses.API /ip4/0.0.0.0/tcp/5001
```
(optional) configure CORS so that Web UI can access API
```
ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["http://167.71.11.220:5001", "http://simple.jora.rocks:5001", "http://ipfs.jora.rocks:5001", "http://127.0.0.1:5001"]'
ipfs config --json API.HTTPHeaders.Access-Control-Allow-Methods '["PUT", "GET", "POST"]'
```


daemon is a console app so we start it in a separate screen session
```
screen -S ipfs_daemon -d -m ipfs daemon
```
Now you should be able to open IPFS GUI to see the status of the node
```
http://<hostname>:5001/webui
```

### Mount ipfs to the linux filesystem

```
sudo mkdir /ipfs /ipns
sudo chown $(whoami) /ipfs /ipns
ipfs daemon &
ipfs mount
```


## IPFS commands

get info about the node (ID, public key,, version, API endpoints)
```
ipfs id
```

get the list of connected peers
```
ipfs swarm peers
```

get peer count
```
ipfs swarm peers | wc -l
```
Get current config
```
ipfs config show
```
Add object(s) to IPFS (-r flag is recursive add of a folder)
```
ipfs add -r /path/to/folder
```
Get object from IPFS
```
ipfs get <hash>
```
Get info about object
```
ipfs object get <hash> | jq
```
Print contents of the file
```
ipfs cat <hash>
```



-----------------
## setup private swarm

On each node:
```
ipfs init
ipfs config --json Datastore.NoSync true
ipfs config Reprovider.Interval "0"
ipfs bootstrap rm --all
```

start the daemon without auto-routing and get multiaddress of the node:
```
screen -S ipfs_daemon -d -m ipfs daemon --routing=none
ipfs id
```
On each node, add multiaddress of another node to bootstrap nodes so they can see each other
```
ipfs bootstrap add MULTIADDR-OF-PROVIDER
```
------------------------
## Publish website
make daemon HTTP gateway listen on public interface
```
ipfs config Addresses.Gateway /ip4/0.0.0.0/tcp/80
```
then restart ipfs daemon (kill then start it again)
```
ipfs add -r site/
```
now it should be accessible via 
`http://<node ip>:8080/ipfs/<hash>`

publish ipns entry
```
ipfs name publish <hash>
```
now it is accessible via static URL
`http://<node ip>:8080/ipns/<node id>`

Once you change site contents:
```
ipfs add -r site/
ipfs name publish <new site hash>
```

Note! TTL for such publish is 12 hours

### Publish different sites under different keys on one node
```
ipfs key gen --type=rsa --size=2048 dog-site
ipfs add ./my-dog-site
ipfs name publish --key=dog-site <hash of dog folder>
```
### Add DNS entry for the website

You can bind IPNS to a domain by adding a DNS TXT record of the form

`dnslink=/ipns/<key>` (if you published it under a ipns key)

`dnslink=/ipfs/<hash>` (to access directly by content hash)

so you can have paths like `/ipns/example.com/2015/09/15/hosting-a-website-on-ipfs/`.

### You can make the link even more readable!

create a DNS TXT record for _dnslink.your.domain

And you will be able to access the site under your.domain

That's it!

--------------------------
Public node:

http://167.71.11.220:5001/webui

http://ipfs.jora.rocks:5001/webui

http://167.71.11.220/ipfs/QmUoVyiSy4ZoGxDh8B4Mip9QX9ZCTPqyZwtgz6uJLcx5K6

http://167.71.11.220/ipns/QmcRoCPijoMHvrCQbC57V7VK7sUqgTiPS3GGofmfkyc1JA

http://ipfs.jora.rocks/ipns/QmcRoCPijoMHvrCQbC57V7VK7sUqgTiPS3GGofmfkyc1JA

http://ipfs.jora.rocks/ipns/simple.jora.rocks


http://simple.jora.rocks/

Access the site from other gateways!

https://ipfs.io/ipns/simple.jora.rocks

https://ipfs.io/ipns/QmcRoCPijoMHvrCQbC57V7VK7sUqgTiPS3GGofmfkyc1JA

