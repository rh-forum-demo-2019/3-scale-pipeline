# Environment Setup

## Pre-requisites

- OpenShift Cluster
- Linux or Mac Workstation
- [3scale SaaS Tenant](https://www.3scale.net/signup)

## 3scale SaaS Environment

- Go to your 3scale SaaS Admin console
- [Generate a new Access Token](https://access.redhat.com/documentation/en-us/red_hat_3scale/2-saas/html/accounts/tokens) that has **write access** to the **Account Management API**
- Save the generated access token for later use:

```sh
export SAAS_ACCESS_TOKEN=123...456
```

- Save the name of your 3scale tenant (the string before `-admin.3scale.net` in your Admin Console) for later use

```sh
export SAAS_TENANT=mdiwing-redhat
```

- Navigate to **Audience** > **Accounts** > **Listing**
- Click on **Developer**
- Saver the **Developer** Account ID that is the last part of the URL (after **/buyers/accounts/**)

```sh
export SAAS_DEVELOPER_ACCOUNT_ID=2445582535751
```
