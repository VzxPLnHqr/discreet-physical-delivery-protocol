# discreet-physical-delivery-protocol
Specifications for a Semi-private Snail Mail Network. Implementation via Bitcoin &amp; Lightning.

## The Problem
Purchaser Paul wants to, in a reasonably discreet manner, buy a trinket from 
Merchant Mary by visiting Mary's website. For digital trinkets Paul can pay some
sats via Bitcoin's lightning network and achieve some reasonable privacy.

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

## Some high-level UI/UX goals
* fit it into the traditional e-commerce workflow as much as possible
* bonus points if the shipping protocol API offered by a network Courier is essentially the same as that offered by existing large shipping/logisitcs companies

## ACKs & Links
By no means do we claim to be the first to think about these things. Therefore, as
we research and come across prior art along these same line, this section will
attempt to credit the original creators of protocols which inspired the one(s)
contemplated herein.

[1] https://wiki.ion.radar.tech/tech/research/hodl-invoice

[2] https://cointelegraph.com/news/ledger-data-leak-a-simple-mistake-exposed-270k-crypto-wallet-buyers
