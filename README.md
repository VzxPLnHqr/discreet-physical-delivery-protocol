# discreet-physical-delivery-protocol
Specifications for a Semi-private Snail Mail Network. Implementation via Bitcoin &amp; Lightning.

## Summary 
Bitcoin's Lightning Network is a major user experience improvement for using bitcoin in a more traditional
ecommerce setting. However, when buying something which requires physical delivery, you still need to to divulge your physical ship-to address to the merchant, which feels a bit antithetical and, ultimately, unnecessary. Can we figure out how to complement the Lightning Network with a discreet physical delivery protocol?

## Status (as of September 2021): REQUIRES MORE DEEP THINKING
See mailing list discussion. Bitcoin/lightning developer ZmnSCPxj pointed out that the on-demand nature of the protocol contemplated here
may make it difficult to implement in the real world. This is primarily because packages may be in transit, and hence unsettled invoices 
might be outstanding, longer than the existing lightning network will allow. This is unfortunate and requires some deep thought. 

It may be worth considering piggybacking on top of [iris.to](https://iris.to) and/or [nostr](https://github.com/fiatjaf/nostr) and/or [sphinx.chat](https://sphinx.chat).
as the coordination layer. These projects seem to be tackling the censorship-resitance distributed messaging challenges. @fiatjaf's Nostr protocol may be closest to what we need.

## Contributing
We welcome contributions and collaboration. Do you have some ideas about how to improve this protocol? Perhaps you simply have questions? Please reach out, open an issue, etc. Open and respectful discussion and iteration is what will allow us to hone in on a useful protocol.

### Discussion
* Mailing List Discussion (Lightning-Dev): https://lists.linuxfoundation.org/pipermail/lightning-dev/2021-June/003065.html

## The Problem
Purchaser Paul wants to, in a reasonably discreet manner, buy a trinket from 
Merchant Mary by visiting Mary's website. For digital trinkets Paul can pay some
sats via Bitcoin's Lightning Network and achieve some reasonable privacy. The item is
delivered digitally (perhaps Paul is using a VPN, Tor, etc), and the payment is onion
routed through the Lightning Network.

However, if the item Paul is purchasing requires Mary to ship it to him in the
real world, then much of the privacy afforded by paying with lightning is eroded
as soon as he puts in his shipping address. Even if he does not use his real name
and (conveniently) has access to a street address less associated with him, Mary
would still learn some information which might help her identify Paul. Yet,
there is usually no real reason why Mary even needs to know who Paul is or the 
final destination of the package.

Additionally, in the traditional e-commerce world, if Merchant Mary's systems are
hacked and or her customer data leaked, then prying eyes now know who bought
what and where the item was shipped to. If Mary is selling something like
hardware wallets[2], this is a big problem, not just for Mary, but for her
customers too. Why try to identify the locations of one hardware wallet at a
time when you can hack the seller and find the ship-to locations of 270,000 of 
them in one database?

Clearly, something needs to change. Purchasers and Merchants both deserve a
shipping method which is (perhaps barely) more private-by-default.

## The (Start) of a Solution
There has been mention of HODL-invoices as a possible solution for atomic,
multi-party item delivery[1], and we would like to explore this further. Such
a protocol seems to address some of the incentives around the physical delivery
component, but may need to be tweaked or optimzed to enhance privacy.

### With A Single Courier
Imagine that Paul and Mary both have an account with Courier Charlie. Then, the
following protocol seems to be a step in the right direction:
1.  Paul asks Charlie to generate a new `parcelid` (which might, conveniently, be a public key of some sort). Paul gives `parcelid` to Mary who can independently confirm with Charlie that it is valid.
2.  Because Charlie knows the final destination (Paul's house) and also the starting point (Mary's shop), Charlie can provide a shipping quote to Mary.
2.  Paul generates `preimage` and sends `hash` of `preimage` to Mary 
2.  Mary creates a hodl invoice `invoice0` with `hash`. The amount of the invoice includes the cost of shipment as quoted by Charlie. Paul pays `invoice0`, but Mary cannot yet settle it because `preimage` is still unknown to Mary.
3.  Mary now sends `hash` to Charlie and Charlie creates another hodl invoice `invoice1` (for delivery costs). Mary pays it and gives the physical package to Charlie.
4.  Charlie now has the package and delivers it to Paul.
5.  Upon delivery, Paul gives `preimage` to Charlie who now can use it to settle his outstanding invoice (`invoice1`) with Mary, thereby revealing `preimage` to Mary who then settles her outstanding `invoice0` with Paul.

#### Analysis
In the above protocol, it is Charlie who (presumably) knows both Paul and Mary's geographic locations. However, from the perspective of Paul, this could still be an improvement over the traditional method of directly giving Mary his physical location. Charlie could be a larger company than Mary and his reputation might depend on being not only an efficient delivery mechanism, but also a discreet one. Charlie might also have better data security than Mary. Afterall, in our scenario, Mary is just selling trinkets and does not really want to think about these sorts of logistical things -- she is just happy to be paid and does not care who bought the item.

Of course, things deteriorate rapidly if Charlie does not exist independent of both Paul and Mary. If Mary plays the role of Charlie, then the protocol essentially degrades to the traditional ecommerce method where the merchant knows the final destination of the package. If Paul plays the role of Charlie, then it basically degrades to the traditional "use an address that is not your personal mailing address" method. In both cases, outside of managing the added complexity itself (the back-n-forths with the "Courier" node, etc), no party seems worse off by adding this shipping/logistics protocol into their deal. 

However, if the Courier node (Charlie) is in fact independent of Paul and Mary, and especially if there is more than one Courier node (perhaps Paul has his preferred Courier node, and Mary has hers, and Charlie is simply somewhere in the middle), then things might begin to change for the better and get a little more private for all. We will explore such a scenario in the next section.

### With Two Couriers

### With N Couriers

### Observation About Physically Labeling Packages
We are used to addressing physical mail (envelopes/packages) with a street address
or some sort of geospatial coordinate system. However, this may not be strictly necessary.
Instead a package could perhaps be "addressed" with an ephemeral identifier which exists
only for the lifetime of this package while it is in transit. Perhaps the package is assigned a
Tor hidden service and that hidden service is what is "printed" on the label/envelope. Perhaps it is
simply assigned a public key. Clearly the details here have not yet been worked out.

## Some open technical questions
1. **Are HODL-invoices workable in practice?** There seems to be some concern [3] that hodl invoices as they currently exist on the lightning network are priced incorrectly and hence are not really usable yet for these types of protocols. A successful shipping-over-lightning protocol as contemplated herein, due to its heavy reliance on hodl invoices (which might be outstanding for the duration of the shipping time of the physical good) could confuse some non-participating nodes into thinking they have been fed transactions which will never settle. This needs to be investigated.
2.  **Do current Lightning implementations have long enough expiration times for on-demand ecommerce?** Preliminary mailing list discussion seems to indicate that most implementations would not accept, for example, a 90 day hold-invoice.
2. **Can Taproot help at all?** After November, 2021 it is anticipated that the bitcoin network will have fully activated Taproot. This may enable some features on lightning (if so, which ones?) that may resolve some of the issues around hodl invoices, etc? Preliminary mailing list discussion seems to indicate that taproot does not necessarily resolve this outstanding hodl-invoice issue, but that some deep thinking around this topic may help.
3. **How to coordinate the Courier nodes (i.e. the routing problem)?** Perhaps, as a start, a couple "meta couriers" operate essentially a (tor hidden service?) bulletin board where other couriers can view/post hashes of the `parcelids` they can route to? Since `parcelids` are single-use (have an expiration?), this might be workable. Still need to get the incentives correct. Eventually this portion could be replaced with a p2p protocol.
4. **Allow Purchaser to delegate finalization of transaction (perhaps to Courier)?** Right now the protocol would require that the Purchaser be avaialbe to give `preimage` to Courier. However, in the traditional package-delivery world, it is often more convenient to allow the Courier to simply leave the package on the doorstep, etc. Obviously, this requires more trust in the Courier, but since you are already trusting the courier with your physical location/address, perhaps that is not a problem? Yet, giving the Courier `preimage` too early will allow Courier to settle its invoice with Merchant. If Merchant has not even shipped the item yet, then there is some moral hazard here.

## Some high-level UI/UX goals
* fit it into the traditional e-commerce workflow as much as possible
* bonus points if the shipping protocol API offered by a network Courier is essentially the same as that offered by existing large shipping/logisitcs companies
* for someone who sells items intermittently, such as artwork, it should be extremely easy to put up a simple website, offer the item for sale, collect lightning as payment and a `parcelid` as shipping address, and done.
* create some end-to-end examples for people to get started quickly
* do not try to do too much -- this is a shipping/logistics protocol, not a full e-commerce protocol

## ACKs & Links
By no means do we claim to be the first to think about these things. Therefore, as
we research and come across prior art along these same line, this section will
attempt to credit the original creators of protocols which inspired the one(s)
contemplated herein.

[1] https://wiki.ion.radar.tech/tech/research/hodl-invoice

[2] https://cointelegraph.com/news/ledger-data-leak-a-simple-mistake-exposed-270k-crypto-wallet-buyers

[3] https://lists.linuxfoundation.org/pipermail/lightning-dev/2021-February/002958.html

[4] Here's a "thought piece modeled after a scientific publication from the year 2030" regarding private delivery networks. It presents some similar ideas: [thought piece](https://www.media.mit.edu/posts/nomadic-systems/) (thanks to @mmalmi for contributing the link)

### Other attempts

[4] https://medium.com/paket-global -- this project seems to have gone dark. From what we gather, they were attempting to do it more on-chain (not using bitcoin or lightning).
