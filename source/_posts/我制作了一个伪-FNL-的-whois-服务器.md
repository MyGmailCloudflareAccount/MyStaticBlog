---
title: 我制作了一个伪 FNL 的 whois 服务器
date: 2026-03-30 10:31:28
---

起因是，我把 whois.rr.kg 这个域名抢了，然后打算写点什么...

成品展示：https://whois.rr.kg/

服务跑在 Serv00 上

源代码：

```js
import express from "express";
import ejs from "ejs";

const app = express();

app.engine("ejstxt", ejs.renderFile);
app.set("view engine", "ejstxt");
app.use(express.static("public"));

app.get("/:domain", async (req, res) => {
    const domain = req.params.domain;
    const whoisServer = "https://domain.nodeloc.com/api/public/whois/";
    const currentHost = req.get("host");

    res.header("Content-Type", "text/plain; charset=utf-8");

    try {
        const resp = await fetch(whoisServer + domain);
        const data = await resp.json();

        if (data.error) {
            return res.status(400).send(`Error: ${data.error}`);
        }

        res.render("template.ejstxt", {
            data: data,
            host: currentHost,
            timestamp: new Date().toISOString(),
        });
    } catch (error) {
        console.error(error);
        res.status(500).send("Internal Server Error");
    }
});

app.listen(3000, () => console.log("Server started"));
```

```ejs
Domain Name: <%= data.full_domain || "N/A" %>
Registry Domain ID: NOT_APPLICABLE
Registrar WHOIS Server: <%= host %>
Registrar URL: https://domain.nodeloc.com
Updated Date: <%= data.registered_at || "N/A" %>
Creation Date: <%= data.registered_at || "N/A" %>
Registry Expiry Date: <%= data.expires_at || "N/A" %>
Registrar: Nodeloc Free Domain
Registrar IANA ID: Not Provided
Domain Status: <%= data.status || "inactive" %> (https://icann.org/epp#<%= data.status || "inactive" %>)

Registry Registrant ID: REDACTED_FOR_PRIVACY
Registrant Name: REDACTED_FOR_PRIVACY
Registrant Organization: REDACTED_FOR_PRIVACY

<% if (data.nameservers && data.nameservers.length > 0) { -%>
<% data.nameservers.forEach(ns => { -%>
Name Server: <%= ns %>
<% }); -%>
<% } else { -%>
Name Server: Not Provided
<% } -%>

DNSSEC: unsigned

---
### Additional System Metadata ###
Days Until Deletion: <%= data.days_until_deletion ?? "null" %>
Days Until Suspension: <%= data.days_until_suspension ?? "null" %>
First Failed At: <%= data.first_failed_at ?? "null" %>
Suspension Reason: <%= data.suspend_reason ?? "null" %>
Suspended At: <%= data.suspended_at ?? "null" %>

### Health & Scan Reports ###
<% if (data.scan) { -%>
Overall Health: <%= data.scan.overall_health || "N/A" %>
DNS Status: <%= data.scan.dns_status || "N/A" %>
HTTP Status: <%= data.scan.http_status || "N/A" %>
SSL Status: <%= data.scan.ssl_status || "N/A" %>
Safe Browsing: <%= data.scan.safe_browsing_status || "N/A" %>
VirusTotal: <%= data.scan.virustotal_status || "N/A" %>
Uptime: <%= data.scan.uptime_percentage ? data.scan.uptime_percentage.toFixed(2) + "%" : "N/A" %>
Last Scanned: <%= data.scan.last_scanned_at || "N/A" %>
<% } else { -%>
Scan Status: No scan data available
<% } -%>

>>> Last update of WHOIS database: <%= timestamp %> <<<
```
