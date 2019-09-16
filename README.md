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
export SAAS_DEVELOPER_ACCOUNT_ID=2445582746132
```

## Generate the 3scale toolbox secret

- First, [install the 3scale toolbox locally](https://github.com/3scale/3scale_toolbox#installation).
- Then, create a secret that contains all your [3scale remotes](https://github.com/3scale/3scale_toolbox/blob/master/docs/remotes.md):

```sh
3scale remote add 3scale-saas "https://$SAAS_ACCESS_TOKEN@$SAAS_TENANT-admin.3scale.net/"
3scale remote add 3scale-onprem "https://$ONPREM_ACCESS_TOKEN@$ONPREM_ADMIN_PORTAL_HOSTNAME/"
oc create secret generic 3scale-toolbox -n "$TOOLBOX_NAMESPACE" --from-file="$HOME/.3scalerc.yaml"
```

## Deploy APIcast instances

- Define your wildcard routes:

```sh
export APICAST_SELF_MANAGED_STAGING_WILDCARD_DOMAIN=saas-staging.$OPENSHIFT_ROUTER_SUFFIX
export APICAST_SELF_MANAGED_PRODUCTION_WILDCARD_DOMAIN=saas-production.$OPENSHIFT_ROUTER_SUFFIX
```

- Deploy APIcast instances (in the project of your choice) to be used with 3scale SaaS as self-managed instances:

```sh
oc create secret generic 3scale-tenant --from-literal=password=https://$SAAS_ACCESS_TOKEN@$SAAS_TENANT-admin.3scale.net
oc create -f https://raw.githubusercontent.com/3scale/apicast/v3.4.0/openshift/apicast-template.yml
oc new-app --template=3scale-gateway --name=apicast-staging -p CONFIGURATION_URL_SECRET=3scale-tenant -p CONFIGURATION_CACHE=0 -p RESPONSE_CODES=true -p LOG_LEVEL=info -p CONFIGURATION_LOADER=lazy -p APICAST_NAME=apicast-staging -p DEPLOYMENT_ENVIRONMENT=sandbox -p IMAGE_NAME=quay.io/3scale/apicast:v3.4.0
oc new-app --template=3scale-gateway --name=apicast-production -p CONFIGURATION_URL_SECRET=3scale-tenant -p CONFIGURATION_CACHE=60 -p RESPONSE_CODES=true -p LOG_LEVEL=info -p CONFIGURATION_LOADER=boot -p APICAST_NAME=apicast-production -p DEPLOYMENT_ENVIRONMENT=production -p IMAGE_NAME=quay.io/3scale/apicast:v3.4.0
oc scale dc/apicast-staging --replicas=1
oc scale dc/apicast-production --replicas=1
oc create route edge apicast-staging --service=apicast-staging --hostname="wildcard.$APICAST_SELF_MANAGED_STAGING_WILDCARD_DOMAIN" --insecure-policy=Allow --wildcard-policy=Subdomain
oc create route edge apicast-production --service=apicast-production --hostname="wildcard.$APICAST_SELF_MANAGED_PRODUCTION_WILDCARD_DOMAIN" --insecure-policy=Allow --wildcard-policy=Subdomain
```
