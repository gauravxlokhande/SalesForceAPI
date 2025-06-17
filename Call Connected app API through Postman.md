# To get an **access token** in Salesforce using a **Connected App** with a **Client ID** and **Client Secret**, you need to use the **OAuth 2.0** flow — typically, the **"Client Credentials"** or **"Username-Password"** flow if you're using Postman.

Here's how you can do it using **Postman**:

---

### ✅ **Step 1: Prerequisites**

Ensure your Salesforce Connected App has the following:

* **OAuth scopes** like `Access and manage your data (api)`
* **Enabled OAuth settings**
* **Callback URL** (even if not used for Username-Password flow)
* `client_id` (Consumer Key) and `client_secret` (Consumer Secret)

---

### 🔐 **Option 1: Username-Password Flow (most common for server-to-server auth)**

**POST Request to:**

```
https://login.salesforce.com/services/oauth2/token
```

(Use `https://test.salesforce.com` for **sandbox**)

**Body (x-www-form-urlencoded):**

| Key            | Value                            |
| -------------- | -------------------------------- |
| grant\_type    | password                         |
| client\_id     | YOUR\_CONSUMER\_KEY              |
| client\_secret | YOUR\_CONSUMER\_SECRET           |
| username       | YOUR\_SALESFORCE\_USERNAME       |
| password       | YOUR\_PASSWORD + SECURITY\_TOKEN |

> 🔐 Note: You must **append your Salesforce security token** to your password unless your org's IP is whitelisted.

---

### 📥 **Response:**

A successful request returns:

```json
{
  "access_token": "00Dxx0000000012!AQwAQF...",
  "instance_url": "https://yourInstance.salesforce.com",
  "token_type": "Bearer",
  "issued_at": "1625641729000",
  "signature": "..."
}
```

You can now use the `access_token` in the `Authorization` header for API calls:

```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

---

### ⚠️ **Troubleshooting Tips:**

* Make sure the user’s profile has **API enabled**.
* Make sure the app is **approved and not restricted by IP**.
* If you get `invalid_grant`, double-check credentials and security token.

---


### 🚀 **Salesforce Platform APIs Collection**

This collection provides over 230 requests covering various Salesforce APIs, including REST, Bulk, Metadata, and more. It also includes authentication flows, such as the Username-Password OAuth flow, which is commonly used for server-to-server integrations.

🔗 **Access the Collection:**

👉 [Salesforce Platform APIs on Postman](https://www.postman.com/salesforce-developers/salesforce-developers/collection/b32utmu/salesforce-platform-apis)

---

### 🛠️ **Steps to Use the Collection**

1. **Fork the Collection:**

   * Click the **Fork** button to add the collection to your workspace.

2. **Set Up Environment Variables:**

   * Define the following variables in your environment:

     * `client_id`: Your Connected App's Consumer Key.
     * `client_secret`: Your Connected App's Consumer Secret.
     * `username`: Your Salesforce username.
     * `password`: Your Salesforce password.
     * `security_token`: Your Salesforce security token (if required).
     * `login_url`: Use `https://login.salesforce.com` for production or `https://test.salesforce.com` for sandboxes.

3. **Authenticate and Obtain Access Token:**

   * Navigate to the **Auth** tab of the collection.
   * Select **OAuth 2.0** as the authorization type.
   * Click **Get New Access Token**.
   * Enter the necessary details:

     * **Grant Type**: `Password Credentials`
     * **Client ID**: `{{client_id}}`
     * **Client Secret**: `{{client_secret}}`
     * **Username**: `{{username}}`
     * **Password**: `{{password}}{{security_token}}`
     * **Auth URL**: `{{login_url}}/services/oauth2/token`
   * Click **Request Token** and then **Use Token**.

4. **Make API Requests:**

   * With the access token obtained, you can now make authenticated API requests to Salesforce.

---

### 📘 **Additional Resources**

* **Postman Documentation on OAuth 2.0 Authentication:** [Learn More](https://learning.postman.com/docs/sending-requests/authorization/oauth-20/)
* **Salesforce Trailhead Guide on Connecting Postman to Salesforce:** [Set Up and Connect Postman](https://trailhead.salesforce.com/content/learn/projects/quick-start-connect-postman-to-salesforce/set-up-and-connect-postman)

---


# Here is a `cURL` command you can use to get an **OAuth 2.0 access token** from Salesforce using the **Username-Password flow**.

---

### 🔐 **cURL for Access Token (Salesforce Username-Password Flow)**

```bash
curl --location --request POST 'https://login.salesforce.com/services/oauth2/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=password' \
--data-urlencode 'client_id=YOUR_CLIENT_ID' \
--data-urlencode 'client_secret=YOUR_CLIENT_SECRET' \
--data-urlencode 'username=YOUR_USERNAME' \
--data-urlencode 'password=YOUR_PASSWORDYOUR_SECURITY_TOKEN'
```

> ✅ Replace:
>
> * `YOUR_CLIENT_ID` = Connected App Consumer Key
> * `YOUR_CLIENT_SECRET` = Connected App Consumer Secret
> * `YOUR_USERNAME` = Your Salesforce username
> * `YOUR_PASSWORD` = Your Salesforce password
> * `YOUR_SECURITY_TOKEN` = Your security token (append it directly after the password)

If you're using a **sandbox**, replace the URL with:

```bash
https://test.salesforce.com/services/oauth2/token
```

---

### 📥 **Response Example:**

```json
{
  "access_token": "00Dxx0000001gZMEAY...",
  "instance_url": "https://yourInstance.salesforce.com",
  "id": "https://login.salesforce.com/id/00Dxx0000001gZM/005xx000001S0kZAAS",
  "token_type": "Bearer",
  "issued_at": "1686943845957",
  "signature": "Xxxyyyzzz123456"
}
```

You can now use this access token to make authenticated API calls like this:

---

### 🔄 **Example Authenticated API Call**

```bash
curl --request GET 'https://yourInstance.salesforce.com/services/data/v58.0/sobjects/Account/' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN'
```

---




# Curl to post the account in standard format (json/Xml)

```
curl --request POST 'https://yourInstance.salesforce.com/services/data/v60.0/sobjects/Account/' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data-raw '{
    "Name": "Test Account",
    "Phone": "1234567890",
    "Website": "https://example.com",
    "BillingCity": "New York"
}'
```

```
curl --request POST 'https://yourInstance.salesforce.com/services/data/v60.0/sobjects/Account/' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
--header 'Content-Type: application/xml' \
--header 'Accept: application/xml' \
--data-raw '<?xml version="1.0" encoding="UTF-8"?>
<Account>
    <Name>Test Account XML</Name>
    <Phone>1234567890</Phone>
    <Website>https://example.com</Website>
    <BillingCity>New York</BillingCity>
</Account>'
```

## To get the Access token in postman:
<img width="708" alt="Screenshot 2025-06-17 at 9 03 28 AM" src="https://github.com/user-attachments/assets/d6853bd1-ac52-42e8-a5d5-9421fc410ee4" />

## ater getting access token in postman use below curl:
```bash
curl --request GET 'https://yourInstance.salesforce.com/services/data/v58.0/sobjects/Account/' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN'
```
