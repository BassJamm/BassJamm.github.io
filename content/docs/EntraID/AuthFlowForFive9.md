---
title: Authentication Flow for Five9
---

## Summary of the Authentication Process

1. **OAuth via Client Credentials Grant**:
   - **Five9** uses the **client credentials grant** flow of OAuth 2.0 to authenticate with **Dynamics 365**.
   - This involves Five9 using its own **client ID** and **client secret** (which might be registered as an app in **Entra ID** or another identity provider).
   - **Dynamics 365** provides an access token in response to this authentication.

2. **Direct API Queries**:
   - **Five9** uses the obtained access token to make direct API requests to **Dynamics 365**.
   - These API calls are made to interact with Dynamics 365 data, such as retrieving, updating, or managing CRM records.

### Key Points:

- **Client Credentials**: Five9 uses its registered application's credentials (client ID and client secret) for OAuth authentication.
- **Direct Communication**: After obtaining the access token, Five9 directly queries the Dynamics 365 API.
- **No User Interaction**: The process involves server-to-server communication without end-user involvement, making it suitable for backend integrations.

### Example Flow:

1. **Authenticate**:
   - Five9 sends a request to Dynamics 365’s OAuth server with its client credentials.
   - Dynamics 365 returns an access token.

2. **Query API**:
   - Five9 uses the access token in the HTTP Authorization header to make requests to the Dynamics 365 API.
   - The API responds with the requested data or performs the necessary actions based on Five9's queries.

Your interpretation captures the essence of how Five9 integrates with Dynamics 365 using OAuth 2.0 and how it queries the Dynamics API directly using the obtained tokens.
