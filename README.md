# Shadowrocket configuration

A rule-based configuration for Shadowrocket. This repository does not provide proxy servers, subscriptions, or credentials. `PROXY` uses the node selected in Shadowrocket.

| File | Purpose | Default |
| --- | --- | --- |
| [`peizhi.conf`](peizhi.conf) | Main service-routing configuration | Apple: `DIRECT`; Telegram: `PROXY`; mainland GeoIP: `DIRECT`; unmatched traffic: `PROXY` |
| [`modules/apns-proxy.sgmodule`](modules/apns-proxy.sgmodule) | Optional Apple Push Notification service (APNs) proxy rules | Not included or enabled by the main configuration |

## Install or update the main configuration

Main configuration URL:

```text
https://raw.githubusercontent.com/521ox/shadowrocket-config/main/peizhi.conf
```

1. Export your current configuration before replacing it. Updating a remote configuration can overwrite local edits.
2. In Shadowrocket, open **Configuration**, add the URL, download it, and select **Use Configuration**. For an existing remote profile, update it, then use/compile it again to refresh referenced rule sets.
3. Set **Global Routing** to **Configuration**, select a working proxy node, and connect. Other global modes or enabled modules can override the intended routing.
4. Check compilation and rule-set download results. A downloaded profile is not proof that every referenced rule was loaded successfully.

The main profile keeps the existing service policies, DNS/IPv6 settings, and node-management approach. Maintenance removes the unavailable `wlxuf/add_rule` references and replaces the old ChatGPT source with the maintained OpenAI category. Cross-client rules use the upstream Shadowrocket format, including companion `DOMAIN-SET` files where required. The unavailable custom amendment lists cannot be reconstructed from their names.

The 18 user-supplied Binance domain suffixes listed at the start of `[Rule]` explicitly use `PROXY` before remote rule sets. Each rule covers the listed domain and its subdomains. This routing list does not verify domain ownership or website safety.

## Optional APNs proxy module

This module is intended for testing cases where foreground app access works but background push notifications fail on the current APNs network path. It is **not** automatically activated by updating `peizhi.conf`.

Module URL:

```text
https://raw.githubusercontent.com/521ox/shadowrocket-config/main/modules/apns-proxy.sgmodule
```

### Scope and trade-offs

- The module routes `push.apple.com`, the specific Apple push host `courier-push-apple.com.akadns.net`, and Apple's published APNs IPv4/IPv6 ranges to `PROXY`.
- It does not proxy the whole `17.0.0.0/8` block, the entire shared `akadns.net` namespace, or all Apple services. It does not change DNS, IPv6 support, tunnel settings, HTTPS decryption, or install certificates or scripts.
- **APNs is a shared system notification path. These rules cannot proxy only Telegram notifications while keeping other apps' notifications direct.** Other apps, including domestic apps, may depend on the same proxy path for push delivery when this module is effective.
- A slow or unavailable proxy can delay or interrupt notifications. This module does not add automatic failover. Actual behavior also depends on your existing Shadowrocket settings.
- A successful test supports a network-path diagnosis; it does not establish deliberate filtering by Apple or any particular operator.

### Enable and test

Menu labels and availability vary by Shadowrocket/iOS version. The [community manual](https://github.com/LOWERTOP/Shadowrocket#模块) is an unofficial UI reference.

1. Save your existing tunnel settings and confirm basic notification permissions, chat mute state, Focus, and Scheduled Summary once. Do not read test messages on another logged-in Telegram client.
2. In **Configuration > Modules**, add the module URL and explicitly enable the module. Keep **Global Routing > Configuration** and use/compile the profile. Inspect other active modules for conflicting rules; module rules normally take precedence over the main profile.
3. Separately check **Settings > Tunnel > Include APNs**. Where required by your version, **Include All Networks** must also be enabled for the APNs option to take effect. A rule file cannot enable this iOS system-level capture setting. Conversely, enabling capture alone does not prove APNs is using `PROXY`; inspect the effective routing.
4. Do not change **Include Local Networks**, **Include Cellular Services**, DNS, IPv6, or other unrelated settings for this test. All-network mode itself can affect device connectivity, Personal Hotspot, or CarPlay. Restore the saved settings if these features stop working.
5. Rebuild existing connections after changing routes: when safe to briefly interrupt connectivity, toggle Airplane Mode, restore your network, and reconnect Shadowrocket. Changing rules alone may leave an old APNs socket on its previous route.
6. Open Telegram once, return normally to the Home Screen without force-quitting it, and lock the phone. Send new, unmuted messages from another account. Compare at least a few messages with the module disabled and enabled; also check a domestic app's notifications. Keep the proxy node and unrelated settings unchanged.
7. Check connection/rule logs for the Apple push domain or published APNs addresses using `PROXY`. A node latency result or a browser request to `push.apple.com` is not a push-delivery test. Missing APNs log entries do not prove missing traffic if the system bypasses the tunnel.
8. Repeat a disabled/enabled/disabled comparison, rebuilding connections each time, to distinguish path dependence from a one-time reconnection recovery. If there is no repeatable improvement, restore the baseline rather than adding broad proxy rules.

### Disable and roll back

1. Disable or remove **APNs Proxy (Optional)** in **Configuration > Modules** and use/compile the main profile again.
2. Restore the tunnel switches recorded before testing, including **Include APNs** and **Include All Networks**. Disabling the module alone does not undo app-level tunnel changes.
3. Rebuild network connections again. With the module inactive and no other overrides, the main profile's Apple-direct policy remains in effect.

To restore the pre-maintenance main file, import this immutable URL. This does not disable separately enabled modules or restore app-level settings:

```text
https://raw.githubusercontent.com/521ox/shadowrocket-config/69ae1710b7a4bbb78128791cdac567ae5b227c5e/peizhi.conf
```

## Verification limits and sources

Repository checks can verify rule structure, policy preservation, APNs range scope, and HTTP availability/content of external lists. They are not Shadowrocket's iOS compiler and cannot establish successful device delivery. Before relying on the profile, verify import/compilation, effective routing, lock-screen notifications, and normal operation of other apps on the phone. Upstream lists follow moving branches and can change after a check.

- [Apple: APNs connections, ports, and network ranges](https://support.apple.com/en-us/102266)
- [Apple: VPN routing and system traffic exceptions](https://developer.apple.com/documentation/networkextension/routing-your-vpn-network-traffic)
- [Apple: network hosts and TLS interception restrictions](https://support.apple.com/en-us/101555)
- [Stash: missing notifications on some network exits and proxy trade-offs](https://stash.wiki/en/faq/ios-push-notifications)
- [Upstream Shadowrocket rule categories and their usage instructions](https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Shadowrocket)
