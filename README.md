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

daemon is a console app so we start it in a separate screen session
```
screen -S ipfs_daemon -d -m ipfs daemon
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
ipfs config Addresses.Gateway /ip4/0.0.0.0/tcp/8080
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

### Publish different sites under different keys on one node
```
ipfs key gen --type=rsa --size=2048 dog-site
ipfs add ./my-dog-site
ipfs name publish --key=dog-site <hash of dog folder>
```
### Add DNS entry for the website

You can bind IPNS to domain by adding a DNS TXT record of the form
`dnslink=/ipns/<peerID>`, so you can have paths like `/ipns/example.com/2015/09/15/hosting-a-website-on-ipfs/`.

------------------------

## install ipfs-pack
```
wget https://dist.ipfs.io/ipfs-pack/v0.6.0/ipfs-pack_v0.6.0_linux-amd64.tar.gz
tar zxvf ipfs-pack_v0.6.0_linux-amd64.tar.gz
cd go-ipfs && ./install.sh
```
