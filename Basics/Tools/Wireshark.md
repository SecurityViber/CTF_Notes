

- ## Profiles 

- Make a lot of sense to setup for different scenarios
- Us this for specific coloring rules or also added filter-buttons 


## Filters 

Some special filters are:
- contains (exact string)
	- frame contains "google"
- matches (regex)
	- http.host matches "\\.(org|com|net)"
- in {range }
	- tcp.port in {80,443}

A very nice way to filter for strings is `frame contains "YourStringHere"`. 



## Misc notes 

Types of TTLs are  64,128, 255


## Another Challenge notes 

1: using `dns` as a filter 
2: using the filter `dns and tcp` -> No results. So everything works via udp
3: All done using the filter `(udp.stream eq 0 ) && (dns.qry.type == 28)`
4: The time delta I could read out of the packet. Its 0.0212438... seconds
5: 