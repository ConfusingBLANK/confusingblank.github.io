# Subdomain Enumeration

## Amass

Basic subdomain enumeration method (`-d`):
```
amass enum -d <domain>
```

Reverse Whois method tries to find other domains with similar whois records(`-whois`):
```
amass intel -d <domain> -whois
```

SSL Certificate Grabbing will grabv the SSL certificate from every IP provided(`-active`):
```
amass intel -active -cidr <network-range>
```

Identify ASNs:
```
amass interl -org "Google"
```

ASNs can be used with the SSL certificate grabbing feature.
```
amass intel -active -asn <asn-number>
```

Putting it all together:
```
amass intel -asn <asn-number> -whois -d <domain>
```

## References
- [https://hakluke.medium.com/haklukes-guide-to-amass-how-to-use-amass-more-effectively-for-bug-bounties-7c37570b83f7](https://hakluke.medium.com/haklukes-guide-to-amass-how-to-use-amass-more-effectively-for-bug-bounties-7c37570b83f7)