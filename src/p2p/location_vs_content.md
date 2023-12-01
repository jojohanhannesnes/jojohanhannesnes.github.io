# Location Based Addressing and Content Based Addressing

location based addressing is like a url, where you need to trust the domain, in a client server model, you can't rely on them, for example, what if there's a DNS issue like notion? or the web server is completely down for some reason. also, if you store something, would it be great to know that you actually own the content? like for example if you want to delete it you just delete it, imagine if its just being soft deleted in their database and the data got leaked

content based addressing uses cryptograhpy to tackle this, in a peer to peer network, we ask for the content instead of just a name or url, its blasted through everyone in the network, and anyone who has it can transfer it to us, imagine it like torrent, first you leech someone who has the file (if they're a seeders) and then once you have the file, or maybe even when you partially owned the file, someone else can leech from you, and you become the seeders

## Content Based Addressing

WE use CID, this ID is a form of codec and multihash