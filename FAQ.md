## Which release should I use for my system?
* NETGEAR WNDR3700v2: mips-hardfloat [#846](https://github.com/Dreamacro/clash/issues/846)
* NETGEAR WNDR3800: mips-softfloat [#579](https://github.com/Dreamacro/clash/issues/579)
* ASUS RT-AC5300: armv5 [#2356](https://github.com/Dreamacro/clash/issues/2356)
* MediaTek MT7620A, MT7621A: mipsle-softfloat ([#136](https://github.com/Dreamacro/clash/issues/136))
* mips_24kc: [#192](https://github.com/Dreamacro/clash/issues/192)

## List of wontfix
The official Clash core project will not implement/fix these things:

* Snell: https://github.com/Dreamacro/clash/issues/2466
* Custom CA: https://github.com/Dreamacro/clash/issues/2333
* VMess Mux: https://github.com/Dreamacro/clash/issues/450
* VLess: https://github.com/Dreamacro/clash/issues/1185
* KCP: https://github.com/Dreamacro/clash/issues/16
* mKCP: https://github.com/Dreamacro/clash/issues/2308
* TLS Encrypted Client Hello: https://github.com/Dreamacro/clash/issues/2295
* TCP support for Clash DNS server: https://github.com/Dreamacro/clash/issues/368
* MITM: https://github.com/Dreamacro/clash/issues/227#issuecomment-508693628

## Time complexity of rule matching
Refer to this discussion: [#422](https://github.com/Dreamacro/clash/issues/422)

## error: unsupported rule type RULE-SET

```
FATA[0000] Parse config error: Rules[0] [RULE-SET,apple,REJECT] error: unsupported rule type RULE-SET
```

You're using Clash open-source edition. Rule Providers is currently only available in the [Premium core](https://github.com/Dreamacro/clash/releases/tag/premium). (it's free)

## I want VLESS support

Short answer: no. Discussion here https://github.com/Dreamacro/clash/issues/1185