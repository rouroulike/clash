Since Clash runs on the Layer 3 (Network Layer), it would be impossible to obtain domain names of the packets for rule-based routing. Hence, we introduced the concept of fake-ip. It enables rule-based routing, minimises the impact of DNS pollution attack and improves network performance, sometimes drastically.

## fake-ip

The concept of "fake IP" addresses is originated from [RFC 3089](https://tools.ietf.org/rfc/rfc3089):

> A "fake IP" address is used as a key to look up the corresponding "FQDN" information.
>
>
The default CIDR for the fake-ip pool is `198.18.0.1/16`, a reserved IPv4 address space, which can be changed in `dns.fake-ip-range`.

When a DNS request is sent to the Clash DNS, the core allocates a _free_ fake-ip_ address_ from the pool, by managing an internal mapping of domain names and their fake-ip addresses.

Take an example of accessing `http://google.com` with your browser.

1. The browser asks Clash DNS for the IP address of `google.com`
2. Clash checks the internal mapping and returned `198.18.1.5`
3. The browser sends an HTTP request to `198.18.1.5` on `80/tcp`
4. When receiving the inbound packet for `198.18.1.5`, Clash looks up the internal mapping and realises the client is actually sending a packet to `google.com`
5. Depending on the rules,
  1. Clash may just send the domain name to an outbound proxy like SOCKS5 or shadowsocks and establish the connection with the proxy server
  2. or Clash might look for the real IP address of `google.com`, in the case of encountering a `SCRIPT`, `GEOIP`, `IP-CIDR` rule, or the case of DIRECT outbound


