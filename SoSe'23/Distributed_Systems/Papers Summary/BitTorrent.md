
## What BitTorrent Does:
When a file is made available using HTTP, all upload cost is placed on the hosting machine. 

With BitTorrent, when multiple people are downloading the same file at the same time, they upload pieces of the file to each other. 

This ==redistributes the cost of upload to downloaders==, (where it is often not even metered), thus making hosting a file with a potentially unlimited number of downloaders affordable.


## Technical Framework:
### Publishing Content:

- To make a file available, a downloader which happens to have the complete file already, known as seed, must be started.

- To start a BitTorrent deployment a static file with the extension .torrent is put on an ordinary web server

- The ==.torrent== contains information about the file, its length, etc and url of a ==tracker==.

**Trackers**:
- Trackers are responsible for helping downloaders find each other
- A downloaders sends information to tracker about what file it is downloading, what port its listening on and similar information.
- Tracker responds with a list of contact information for peers which are downloading the same file.
- Downloaders then use this information to connect to each other


### Peer Distribution:
Trackers are the only way for peers to find each other and the only point of coordination at all, the standard tracker algorithm is to return a random list of peers.

In order to keep track of which peers have what, BitTorrent cuts files into pieces of fixed size, typically a quarter megabyte. ==Each downloader reports to all of its peers what pieces it has==

SHA1 hashes of all the pieces are included in the .torrent file and peers don't report that they have a piece until they have checked the hash.

Peers continuously download pieces from peers which they can.

### Piece Selection:
When selecting which piece to start downloading next, peers generally download pieces which the fewest of their own peers have first, a technique we refer to as ==rarest first==.

For deployments with a single seed whose upload capacity is considerably less than that of many downloaders, performance is much better if different downloaders get different pieces from the seed, since redundant downloads waste the opportunity for the seed to get more information out.

By replicating the rarest pieces as quickly as possible, reduces the risk of them getting completely lost as current peers stop uploading.

## Choking Algorithms:

Choking is a temporary refusal to upload; It stops uploading, but downloading can still happen.

A good choking algorithm should :
- utilise all available resources, 
- provide reasonably consistent download rates for everyone
- be resistant to peers only downloading and not uploading.


### Pareto Efficiency:
Peers reciprocate uploading to peers which upload to them, with the goal of at any time of having several connections which are actively transferring in both directions.


### BitTorrents Choking Algorithm:
BitTorrent peer always unchokes a fixed number of other peers (default is four), so the issue becomes which peers to unchoke.

Decisions as to which peers to un-choke are based directly on current download rate.

### Optimistic Unchoking:
Simply uploading to the peers which provide the best download rate would suffer from having no method of discovering if currently unused connections are better than the ones being used.

To fix this, at all times a BitTorrent peer has a single ‘==optimistic unchoke==’, which is unchoked regardless of the current download rate from it

### Anti Snubbing:
When over a minute goes by without getting a single piece from a particular peer, BitTorrent assumes it is ’snubbed’ by that peer and doesn’t upload to it except as an optimistic unchoke.


### Upload Only:
Once a peer is done downloading, it no longer has useful download rates to decide which peers to up- load to. The current implementation then switches to preferring peers which it has better upload rates to, which does a decent job of utilising all available upload capacity and preferring peers which no-one else happens to be uploading to at the moment.