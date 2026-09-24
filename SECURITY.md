# Security

## Reporting a vulnerability

Report it privately to [operations@webanion.com](mailto:operations@webanion.com). Do not open a public issue, and do not put the details in a pull request or a discussion thread.

A useful report says which package and version, what you found, what an attacker could do with it, and enough detail to reproduce it. A proof of concept helps. If you are not sure whether something is a real issue, send it anyway and say so.

## What to expect

We acknowledge reports within a few working days, usually sooner. After that you get an assessment of whether it is a vulnerability, how serious we think it is and what we intend to do, and we tell you when the fixed version is published.

A fix ships as a new version of the affected package, with the changelog naming the vulnerability class once the fix is out, never before. If you want credit when it ships, say so in your first message. There is no bug bounty and no paid disclosure programme here.

## Scope

These are libraries that other systems deploy, so a vulnerability here is one that a consuming application inherits: an unsafe default, a bypassable guard, a signing or encryption weakness, a path that reaches the network without the SSRF policy, a place where a secret could reach a log. Report those. A weakness in how a particular product uses a library belongs to that product's owner, and if the product is one Webanion runs, report it the same way and we route it.

When you are testing, stay off anything live that you do not own. Do not run denial of service tests against a deployment, do not attempt to access accounts that are not yours, and stop at the point where you have demonstrated the issue rather than exploring what else it opens.

## Credentials in code

If you find a live credential, key or token committed anywhere in this organization, or inside a published package, treat it as a vulnerability and report it by email rather than opening an issue. Say where you saw it and nothing more, and do not test whether it still works.
