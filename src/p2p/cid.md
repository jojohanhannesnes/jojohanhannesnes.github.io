# CID

https://github.com/multiformats/cid

CID is the id or address to specify what is the data.

first thing is to use cyrptographic algo like sha-256 to make sure the data secure, and cannot be temper(produce different hashes), CID uses multihash, i assume because there might be occasionally an algorithm that can be insecure in the future (sha-1, md5), so in order to tackle this, CID support multiple algorithm with multihash

## Multihash

a self describing hash, the format is TLV (type length value), the original hash is prefixed with type and length of the hash

{algorithm: https://github.com/multiformats/multicodec/blob/master/table.csv}
{length: sha-256 will be 256 bits/32 bytes}
{value: the actual value}

this is known as CIDv0, there is CIDv1 which helps us to know what encoding is used for the data that is hashed(maybe JSON, protobuf, etc?)

to differentiate v0 and v1 we prefix again the version in the CID

{version}{encoding(JSON for example)}{algo}{length}{value}

## Multibase

v0 always encoded with base58, but to support multiple base, we add another param in the cid to determine, what base encoding to convert this binary to string


