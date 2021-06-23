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
would still learn some information which might help her identify Paul. We view
this as unnacceptable, especially because there is no real reason why Mary needs
to know who Paul is or the final destination of the package.

Additionally, in the traditional e-commerce world, if Merchant Mary's systems are
hacked and or her customer data leaked, then prying eyes now know who bought
what and where the item was shipped to. If Mary is selling something like
hardware wallets[2], this is a big problem, not just for Mary, but for her
customers too. Why try to identify the locations of one hardware wallet at a
time when you can hack the seller and find the ship-to locations of 270,000 of 
them in one database?

Clearly, something needs to change. Purchaers and Merchants both deserve a
shipping method which is (perhaps barely) more private-by-default.

## The (Start) of a Solution
There has been mention of HODL-invoices as a possible solution for atomic,
multi-party item delivery[1], and we would like to explore this further. Such
a protocol seems to address some of the incentives around the physical delivery
component, but may need to be tweaked or optimzed to enhance privacy.

## ACKs & Links
By no means do we claim to be the first to think about these things. Therefore, as
we research and come across prior art along these same line, this section will
attempt to credit the original creators of protocols which inspired the one(s)
contemplated herein.

[1] https://wiki.ion.radar.tech/tech/research/hodl-invoice

[2] https://cointelegraph.com/news/ledger-data-leak-a-simple-mistake-exposed-270k-crypto-wallet-buyers
