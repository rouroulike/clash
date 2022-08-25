# Changes in Configurations
* `Proxy`, `Proxy Group` and `Rule` is no longer used. They are now respectively `proxies`, `proxy-groups` and `rules`.
* `rule-provider` is no longer a valid keyword. Use `rule-providers` instead.
* For `ss` nodes (remote servers), `obfs` and `obfs-host` are no longer valid fields. Use `plugin` and `plugin-opts` instead.
* `FINAL` in rules is no longer valid. Use `MATCH` instead.
* `SOURCE-IP-CIDR` in rules is no longer valid. Use `SRC-IP-CIDR` instead.
* `experimental` is now deprecated. The former `experimental.interface-name` is now `interface-name`.

For more version history, look at the release notes. I think Dreamacro spent a lot of time to write them. 😆 