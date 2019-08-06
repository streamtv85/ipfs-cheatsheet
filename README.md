# ipfs-cheatsheet
List of useful commands when configuring and working with IPFS nodes


## install IPFS
```
wget https://dist.ipfs.io/go-ipfs/v0.4.20/go-ipfs_v0.4.20_linux-amd64.tar.gz
tar zxvf go-ipfs_v0.4.20_linux-amd64.tar.gz
cd go-ipfs && ./install.sh
```

*install tool for json pretty print*
```
sudo apt-get -y update && sudo apt-get -y install jq
```

init ipfs storage
```ipfs init```


### increase opened files limit
ulimit -n 5560


# daemon is a console app so we start it in a separate screen session
screen -S ipfs_daemon -d -m ipfs daemon

#get info about the node (ID, public key,, version, API endpoints)
ipfs id

#get the list of peers found
ipfs swarm peers
# count the lines
ipfs swarm peers | wc -l

------------------------

# install ipfs-pack
wget https://dist.ipfs.io/ipfs-pack/v0.6.0/ipfs-pack_v0.6.0_linux-amd64.tar.gz
tar zxvf ipfs-pack_v0.6.0_linux-amd64.tar.gz
cd go-ipfs && ./install.sh

-----------------
setup private swarm

ipfs init
ipfs config --json Datastore.NoSync true
ipfs config Reprovider.Interval "0"
ipfs bootstrap rm --all
ipfs bootstrap add MULTIADDR-OF-PROVIDER

# then start the daemon without auto-routing:
screen -S ipfs_daemon -d -m ipfs daemon --routing=none
