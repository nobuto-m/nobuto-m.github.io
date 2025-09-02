---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Tailscale ACL to grants"
slug: tailscale-acl-to-grants
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: []

reading_time: false
show_related: true
share: false

year: 2025
date: 2025-09-02T22:26:40+10:00
lastmod: 2025-09-02T22:26:40+10:00

featured: false
draft: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.png" caption="" >}}

```
// Example/default ACLs for unrestricted connections.
{
	// Declare static groups of users. Use autogroups for all users or users with a specific role.
	// "groups": {
	//      "group:example": ["alice@example.com", "bob@example.com"],
	// },

	// Define the tags which can be applied to devices and by which users.
	"tagOwners": {
		"tag:client":     ["autogroup:admin"],
		"tag:server":     ["autogroup:admin"],
		"tag:scraper":    ["autogroup:admin"],
		"tag:client-lab": ["autogroup:admin"],
		"tag:server-lab": ["autogroup:admin"],
	},

	// Define access control lists for users, groups, autogroups, tags,
	// Tailscale IP addresses, and subnet ranges.
	"acls": [
		// Allow all connections.
		// Comment this section out if you want to define specific restrictions.
		//{"action": "accept", "src": ["*"], "dst": ["*:*"]},
		{
			"action": "accept",
			"src":    ["tag:client"],
			"dst":    ["*:*"],
		},
		{
			"action": "accept",
			"src":    ["tag:client-lab"],
			"dst":    ["tag:server-lab:*"],
		},
		{
			"action": "accept",
			"src":    ["autogroup:shared"],
			"dst":    ["*:*"],
		},
	],

	// Define users and devices that can use Tailscale SSH.
	"ssh": [
		// Allow all users to SSH into their own devices in check mode.
		// Comment this section out if you want to define specific restrictions.
		{
			"action": "check",
			"src":    ["autogroup:members"],
			"dst":    ["autogroup:self"],
			"users":  ["autogroup:nonroot", "root"],
		},
	],

	// Test access rules every time they're saved.
	"tests": [
		{
			// clients can access anywhere
			"src": "tag:client",

			"accept": ["tag:server:22", "tag:client:22", "tag:scraper:22"],
		},
		{
			// scraper can only access to servers with port 9100
			"src": "tag:scraper",

			"accept": ["tag:server:9100"],
			"deny":   ["tag:client:22", "tag:scraper:22", "tag:server:22"],
		},
		{
			// servers cannot access anywhere
			"src": "tag:server",

			"deny": ["tag:client:22", "tag:server:22", "tag:scraper:22"],
		},
		{
			// client-lab can access to server-lab but cannot access anywhere
			"src": "tag:client-lab",

			"accept": ["tag:server-lab:80"],
			"deny":   ["tag:client:22", "tag:server:22", "tag:scraper:22"],
		},
	],

	"grants": [
		{
			"src": ["tag:scraper"],
			"dst": ["tag:server"],
			"ip":  ["9100"],
		},
		{
			"src": ["tag:client-lab"],
			"dst": ["tag:server-lab"],
			"ip":  ["*"],
		},
	],
}
```
