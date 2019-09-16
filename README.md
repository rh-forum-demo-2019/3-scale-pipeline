# 3-scale-pipeline
Environment Setup
Pre-requisites
OpenShift Cluster
Linux or Mac Workstation
3scale SaaS Tenant
3scale SaaS Environment
Go to your 3scale SaaS Admin console
Generate a new Access Token that has write access to the Account Management API
Save the generated access token for later use:
export SAAS_ACCESS_TOKEN=123...456
Save the name of your 3scale tenant (the string before -admin.3scale.net in your Admin Console) for later use
export SAAS_TENANT=nmasse-redhat
Navigate to Audience > Accounts > Listing
Click on Developer
Saver the Developer Account ID that is the last part of the URL (after /buyers/accounts/)
export SAAS_DEVELOPER_ACCOUNT_ID=2445582535751
