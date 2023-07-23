# 简介

**V2Ray** 路由规则文件加强版，可代替 V2Ray 官方 `geoip.dat` 和 `geosite.dat`，兼容 [Clash-PlusPro](https://github.com/yaling888/clash)、Shadowsocks-windows、Xray-core、Trojan-Go 和 leaf。

## 规则文件生成方式

### geoip.dat

- 通过仓库 [@yaling888/geoip](https://github.com/yaling888/geoip) 生成
- 其中全球 IP 地址（IPv4 和 IPv6）来源于 [MaxMind GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)，`CN`（中国大陆）类别下的 IPv4 地址融合了 [ipip.net](https://github.com/17mon/china_ip_list) 和 [@gaoyifan/china-operator-ip](https://github.com/gaoyifan/china-operator-ip)，`CN`（中国大陆）类别下的 IPv6 地址融合了 [MaxMind GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 和 [@gaoyifan/china-operator-ip](https://github.com/gaoyifan/china-operator-ip)
- 新增类别（方便有特殊需求的用户使用）：
  - `geoip:cloudflare`
  - `geoip:cloudfront`
  - `geoip:facebook`
  - `geoip:fastly`
  - `geoip:google`
  - `geoip:netflix`
  - `geoip:telegram`
  - `geoip:twitter`

> 希望定制 `geoip.dat` 文件？查看仓库 [@yaling888/geoip](https://github.com/yaling888/geoip)。

### geosite.dat

- 基于 [@v2fly/domain-list-community/data](https://github.com/v2fly/domain-list-community/tree/master/data) 数据，通过仓库 [@yaling888/domain-list-custom](https://github.com/yaling888/domain-list-custom) 生成
- **加入大量中国大陆域名、Apple 域名和 Google 域名**：
  - [@felixonmars/dnsmasq-china-list/accelerated-domains.china.conf](https://github.com/felixonmars/dnsmasq-china-list/blob/master/accelerated-domains.china.conf) 加入到 `geosite:cn` 类别中
  - [@felixonmars/dnsmasq-china-list/apple.china.conf](https://github.com/felixonmars/dnsmasq-china-list/blob/master/apple.china.conf) 加入到 `geosite:geolocation-!cn` 类别中（如希望本文件中的 Apple 域名直连，请参考下面 [geosite 的 Routing 配置方式](https://github.com/yaling888/geosite#geositedat-1)）
  - [@felixonmars/dnsmasq-china-list/google.china.conf](https://github.com/felixonmars/dnsmasq-china-list/blob/master/google.china.conf) 加入到 `geosite:geolocation-!cn` 类别中（如希望本文件中的 Google 域名直连，请参考下面 [geosite 的 Routing 配置方式](https://github.com/yaling888/geosite#geositedat-1)）
- **加入 GFWList 域名**：
  - 基于 [@gfwlist/gfwlist](https://github.com/gfwlist/gfwlist) 数据，通过仓库 [@cokebar/gfwlist2dnsmasq](https://github.com/cokebar/gfwlist2dnsmasq) 生成
  - 加入到 `geosite:gfw` 类别中，供习惯于 PAC 模式并希望使用 [GFWList](https://github.com/gfwlist/gfwlist) 的用户使用
  - 同时加入到 `geosite:geolocation-!cn` 类别中
- **加入 EasyList 和 EasyListChina 广告域名**：通过 [@AdblockPlus/EasylistChina+Easylist.txt](https://easylist-downloads.adblockplus.org/easylistchina+easylist.txt) 获取并加入到 `geosite:category-ads-all` 类别中
- **加入 AdGuard DNS Filter 广告域名**：通过 [@AdGuard/DNS-filter](https://kb.adguard.com/en/general/adguard-ad-filters#dns-filter) 获取并加入到 `geosite:category-ads-all` 类别中
- **加入 Peter Lowe 广告和隐私跟踪域名**：通过 [@PeterLowe/adservers](https://pgl.yoyo.org/adservers) 获取并加入到 `geosite:category-ads-all` 类别中
- **加入 Dan Pollock 广告域名**：通过 [@DanPollock/hosts](https://someonewhocares.org/hosts) 获取并加入到 `geosite:category-ads-all` 类别中
- **加入 Windows 操作系统相关的系统升级和隐私跟踪域名**：
  - 基于 [@crazy-max/WindowsSpyBlocker](https://github.com/crazy-max/WindowsSpyBlocker/tree/master/data/hosts) 数据
  - [**慎用**] Windows 操作系统使用的隐私跟踪域名 [@crazy-max/WindowsSpyBlocker/hosts/spy.txt](https://github.com/crazy-max/WindowsSpyBlocker/blob/master/data/hosts/spy.txt) 加入到 `geosite:win-spy` 类别中
  - [**慎用**] Windows 操作系统使用的系统升级域名 [@crazy-max/WindowsSpyBlocker/hosts/update.txt](https://github.com/crazy-max/WindowsSpyBlocker/blob/master/data/hosts/update.txt) 加入到 `geosite:win-update` 类别中
  - [**慎用**] Windows 操作系统附加的隐私跟踪域名 [@crazy-max/WindowsSpyBlocker/hosts/extra.txt](https://github.com/crazy-max/WindowsSpyBlocker/blob/master/data/hosts/extra.txt) 加入到 `geosite:win-extra` 类别中
  - 关于这三个类别的使用方式，请参考下面 [geosite 的 Routing 配置方式](https://github.com/yaling888/geosite#geositedat-1)
- **可添加自定义直连、代理和广告域名**：由于上游域名列表更新缓慢或缺失某些域名，所以引入**需要添加的域名**列表。[`hidden 分支`](https://github.com/yaling888/geosite/tree/hidden)里的三个文件 `direct.txt`、`proxy.txt` 和 `reject.txt`，分别存放自定义的需要添加的直连、代理、广告域名，最终分别加入到 `geosite:cn`、`geosite:geolocation-!cn` 和 `geosite:category-ads-all` 类别中
- **可移除自定义直连、代理和广告域名**：由于上游域名列表存在需要被移除的域名，所以引入**需要移除的域名**列表。[`hidden 分支`](https://github.com/yaling888/geosite/tree/hidden)里的三个文件 `direct-need-to-remove.txt`、`proxy-need-to-remove.txt` 和 `reject-need-to-remove.txt`，分别存放自定义的需要从 `direct-list`（直连域名列表）、`proxy-list`（代理域名列表）和 `reject-list`（广告域名列表） 移除的域名

## 规则文件下载及使用方式

**下载地址**：

> 如果无法访问域名 `raw.githubusercontent.com`，可以使用第二个地址（`cdn.jsdelivr.net`），但是内容更新会有 12 小时的延迟。

- **geoip.dat**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/geoip.dat](https://raw.githubusercontent.com/yaling888/geosite/release/geoip.dat)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/geoip.dat](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/geoip.dat)
- **geosite.dat**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/geosite.dat](https://raw.githubusercontent.com/yaling888/geosite/release/geosite.dat)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/geosite.dat](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/geosite.dat)
- **直连域名列表 direct-list.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/direct-list.txt](https://raw.githubusercontent.com/yaling888/geosite/release/direct-list.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/direct-list.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/direct-list.txt)
- **代理域名列表 proxy-list.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/proxy-list.txt](https://raw.githubusercontent.com/yaling888/geosite/release/proxy-list.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/proxy-list.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/proxy-list.txt)
- **广告域名列表 reject-list.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/reject-list.txt](https://raw.githubusercontent.com/yaling888/geosite/release/reject-list.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/reject-list.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/reject-list.txt)
- **Apple 在中国大陆可直连的域名列表 apple-cn.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/apple-cn.txt](https://raw.githubusercontent.com/yaling888/geosite/release/apple-cn.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/apple-cn.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/apple-cn.txt)
- **Google 在中国大陆可直连的域名列表 google-cn.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/google-cn.txt](https://raw.githubusercontent.com/yaling888/geosite/release/google-cn.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/google-cn.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/google-cn.txt)
- **GFWList 域名列表 gfw.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/gfw.txt](https://raw.githubusercontent.com/yaling888/geosite/release/gfw.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/gfw.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/gfw.txt)
- **Windows 操作系统使用的隐私跟踪域名列表 win-spy.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/win-spy.txt](https://raw.githubusercontent.com/yaling888/geosite/release/win-spy.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/win-spy.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/win-spy.txt)
- **Windows 操作系统使用的系统升级域名列表 win-update.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/win-update.txt](https://raw.githubusercontent.com/yaling888/geosite/release/win-update.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/win-update.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/win-update.txt)
- **Windows 操作系统使用的附加隐私跟踪域名列表 win-extra.txt**：
  - [https://raw.githubusercontent.com/yaling888/geosite/release/win-extra.txt](https://raw.githubusercontent.com/yaling888/geosite/release/win-extra.txt)
  - [https://cdn.jsdelivr.net/gh/yaling888/geosite@release/win-extra.txt](https://cdn.jsdelivr.net/gh/yaling888/geosite@release/win-extra.txt)

### geosite.dat

跟 V2Ray 官方 `geosite.dat` 配置方式相同。相比官方 `geosite.dat` 文件，本项目特有的类别：

- `geosite:apple-cn`：包含 [@felixonmars/dnsmasq-china-list/apple.china.conf](https://github.com/felixonmars/dnsmasq-china-list/blob/master/apple.china.conf) 文件里的域名，供希望 Apple 域名直连（不走代理）的用户使用。
- `geosite:google-cn`：包含 [@felixonmars/dnsmasq-china-list/google.china.conf](https://github.com/felixonmars/dnsmasq-china-list/blob/master/google.china.conf) 文件里的域名，供希望 Google 域名直连（不走代理）的用户使用。
- [**慎用**]`geosite:win-spy`：包含 [@crazy-max/WindowsSpyBlocker/hosts/spy.txt](https://github.com/crazy-max/WindowsSpyBlocker/blob/master/data/hosts/spy.txt) 文件里的域名，供希望屏蔽 Windows 操作系统隐私跟踪域名的用户使用。
- [**慎用**]`geosite:win-update`：包含 [@crazy-max/WindowsSpyBlocker/hosts/update.txt](https://github.com/crazy-max/WindowsSpyBlocker/blob/master/data/hosts/update.txt) 文件里的域名，供希望屏蔽 Windows 操作系统自动升级的用户使用。
- [**慎用**]`geosite:win-extra`：包含 [@crazy-max/WindowsSpyBlocker/hosts/extra.txt](https://github.com/crazy-max/WindowsSpyBlocker/blob/master/data/hosts/extra.txt) 文件里的域名，供希望屏蔽 Windows 操作系统附加隐私跟踪域名的用户使用。

> ⚠️ 注意：在 Routing 配置中，类别越靠前（上），优先级越高，所以 `geosite:apple-cn` 和 `geosite:google-cn` 要放置在 `geosite:geolocation-!cn` 前（上）面才能生效。

#### 高级用法

v2fly/domain-list-community 项目 [data](https://github.com/v2fly/domain-list-community/tree/master/data) 目录中某些列表里的规则会被标记诸如 `@cn` 的 attribute（如下所示），意为该域名在中国大陆有接入点，可直连。

```
steampowered.com.8686c.com @cn
steamstatic.com.8686c.com @cn
```

对于玩 Steam 国区游戏，想要直连的用户，可以设置类别 `geosite:steam@cn` 为直连，意为将 [steam](https://github.com/v2fly/domain-list-community/blob/master/data/steam) 列表内所有被标记了 `@cn` attribute 的规则（域名）设置为直连。同理，由于 [category-games](https://github.com/v2fly/domain-list-community/blob/master/data/category-games) 列表包含了 `steam`、`ea`、`blizzard`、`epicgames` 和 `nintendo` 等常见的游戏厂商。设置类别 `geosite:category-games@cn` 为直连，即可节省大量服务器流量。

> ⚠️ 注意：在 Routing 配置中，类别越靠前（上），优先级越高，所以 `geosite:category-games@cn` 等所有带有 `@cn` attribute 的规则都要放置在 `geosite:geolocation-!cn` 前（上）面才能生效。
> 
> `category-games` 列表内的规则（域名）可能会有疏漏，请留意规则命中情况。如发现遗漏，欢迎到项目 v2fly/domain-list-community 提 [issue](https://github.com/v2fly/domain-list-community/issues) 反馈。

## 致谢

- [@yaling888/geoip](https://github.com/yaling888/geoip)
- [@v2fly/domain-list-community](https://github.com/v2fly/domain-list-community)
- [@yaling888/domain-list-custom](https://github.com/yaling888/domain-list-custom)
- [@felixonmars/dnsmasq-china-list](https://github.com/felixonmars/dnsmasq-china-list)
- [@gfwlist/gfwlist](https://github.com/gfwlist/gfwlist)
- [@cokebar/gfwlist2dnsmasq](https://github.com/cokebar/gfwlist2dnsmasq)
- [@AdblockPlus/EasylistChina+Easylist.txt](https://easylist-downloads.adblockplus.org/easylistchina+easylist.txt)
- [@AdGuard/DNS-filter](https://kb.adguard.com/en/general/adguard-ad-filters#dns-filter)
- [@PeterLowe/adservers](https://pgl.yoyo.org/adservers)
- [@DanPollock/hosts](https://someonewhocares.org/hosts)
- [@crazy-max/WindowsSpyBlocker](https://github.com/crazy-max/WindowsSpyBlocker)
