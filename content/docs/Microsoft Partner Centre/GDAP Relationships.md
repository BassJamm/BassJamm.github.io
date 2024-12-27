---
title: GDAP Relationships
---

## What are they?

Granular Delegated Admin Privileges (GDAP) in Partner Center is a security feature that allows partners to have least-privileged, granular, and time-bound access to their customers' workloads, enhancing security and compliance.

## Considerations

- Relationships cannot be extended if you request the Global Administrator role.
- Notifications for creation\deletion of a relationship will be sent to staff who're part of the groups "HelpdeskAgent" and "AdminAgent" in the parent tenant and customer users with the Global Admin role assigned in the customer tenants.
- Azure Access is not affected by this change, that is managed via the AdminAgents group (DAP) in the parent tenant.
- Azure Lighthouse is not affected by this change, it is an unrelated product.
- M365 Lighthouse is linked and affected by this change.

### Admin relationship naming convention

{{< callout type="info" >}}
  Each name must be unique every time a relationship is created.
{{< /callout >}}

Naming convention: Contoso "Customer Name" GDAP "yearFrom" to "yearTo" (added dates so each name in unique).
Example Name: Contoso Customer GDAP 2024 to 2027

### RBAC Roles that need to be requested

Application admin
Authentication admin
Azure AD Joined Device Local admin
Billing Admin
Cloud Application admin
Conditional Access admin
Exchange admin
Global Reader
Groups admin
Helpdesk admin
Intune admin
License admin
Microsoft Entra Joined Device Admin
Office Apps admin
Printer admin
Privileged role admin
Security Reader
Security Admin
Service support admin
SharePoint admin
Skype for Business admin
Teams admin
User admin

## Steps to Request the relationship

{{< callout type="info" >}}
  ideally for step 8 onwards, someone at the customer tenant will accept this.
{{< /callout >}}

1. Within the partner centre, select a customer.
2. Click Admin relationships > request admin relationship.
3. Enter a relationship name and the duration (730) in days. (Max is 730 at present).
4. Select the relevant admin roles to be assigned to this relationship.
5. Finalise the request.
6. Review the request form and update any relevant information.
7. Select Done.
8. Within the customer tenant login with a Global Administrator.
9. Navigate to [https://admin.microsoft.com](https://admin.microsoft.com).
10. Click Settings > Partner relationships.
11. Accept\approve the relationship request.

## Assign Staff (via Groups) to the Roles requested

{{< callout type="info" >}}
  These groups should be created in EntraId and have the staff accounts in who're in need to use this access.
{{< /callout >}}

1. Within the partner center, select a customer.
2. Click Admin relationships.
3. Add the relevant groups listed below to each customer.
4. Assign the roles as documented.
