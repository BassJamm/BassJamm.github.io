---
title: Bypass RDP Licensing
---

If you have installed Remote Desktop Services via the Roles within Windows, and not applied a Licensing Server, or configured the RDS server host, generally after 120 days it will lock you out of the server.

In order to bypass this, you are able to however invoke MSTSC (Microsoft Terminal Services Client) with the `/admin` flag in order to bypass it, and login via a full admin session.

To perform this, open a run prompt as an admin and type in `mtsc /admin:`.

Once this has been invoked with the Admin privileges, you will be able to then login as you normally would, and bypass the lockout. You can then go ahead and either remove, or configure Remote Desktop Services to resolve the issue for normal access.
