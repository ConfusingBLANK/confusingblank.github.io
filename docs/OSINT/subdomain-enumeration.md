# Subdomain Enumeration

## Amass

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
amass intel -org "<company-name>"
```

ASNs (`-asn`) can be used with the SSL certificate grabbing feature(`-active`).
```
amass intel -active -asn <asn-number>
```

Putting it all together:
```
amass intel -asn <asn-number> -whois -d <domain>
```

Basic subdomain enumeration method (`-d`):
```
amass enum -d <domain>
```

## VHOST Discovery with ffuf

Useful for finding subdomains without using DNS records. This method fuzzes the Host header parameter in HTTP requests. The same can be done with Burp Suites Intruder feature.
```
ffuf -w wordlist.txt -u https://site.com/ -H "Host: FUZZ.site.com"
```

In my experience this generates a lot of false positives and requires the use of filters. Identify a commonality in false positives such as amount of words, number of lines, or resenpose size.

> -fw : to filter by the amount of words
> 
> -fl : to filter by the number of lines
> 
> -fs : to filter by the size of the response
> 
> -fc : to filter by the status code
> 
> -fr : to filter by the regex pattern


## References
- [https://hakluke.medium.com/haklukes-guide-to-amass-how-to-use-amass-more-effectively-for-bug-bounties-7c37570b83f7](https://hakluke.medium.com/haklukes-guide-to-amass-how-to-use-amass-more-effectively-for-bug-bounties-7c37570b83f7)
- [https://medium.com/quiknapp/fuzz-faster-with-ffuf-c18c031fc480](https://medium.com/quiknapp/fuzz-faster-with-ffuf-c18c031fc480)