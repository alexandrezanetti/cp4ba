# Installing Cloud Pak for Business Automation Operator

This instructions were build upon [Cloud Pack For Business Automation](https://www.ibm.com/docs/en/cloud-paks/cp-biz-automation/22.0.2) release 22.0.2 running on [OpenShift](https://docs.openshift.com/container-platform/4.12/welcome/index.html) 4.12.

It was assumed that you followed the instructions from [fyre-ocp-cp4x](https://github.com/alexandrezanetti/fyre-ocp-cp4x) repository.

Follow the steps below to install Cloud Pak for Automation Operator onto OpenShift Cluster.

The yaml files on this repository consider the following:

|Parameter|Value|
|:---------------|:----------:|
|anyuid|cp4basauid|
|[username]|cp4ba|
|[password]|cp4ba|
|${NAMESPACE}|cp4a|


#### 1. Move to a directory where you want to put the temp files.
> cd /home

#### 2. Install the htpasswd command
> yum install httpd-tools

#### 3. Create a user
> htpasswd -c -B -b users.htpasswd [username] [password]

#### 4. Verify that it worked.
> htpasswd -b -v users.htpasswd [username] [password]

#### 5. Create a secret to contain the htpasswd file. You must be logged in as the admin user to the cluster.
> oc create secret generic htpass-secret --from-file=htpasswd=./users.htpasswd -n openshift-config

#### 6. Clone this git repository

#### 7. Apply the config file with the htpasswd identity provider settings.
> oc apply -f oauth_htpasswd.yaml

#### 8. Apply the service account to the target namespace.
> oc apply -f service-account-for-starter.yaml -n ${NAMESPACE}

#### 9. Bind the security context constraints (SCC) to control the actions the SA can take and what it can access.
> oc adm policy add-scc-to-user [anyuid] -z ibm-cp4ba-[anyuid] -n ${NAMESPACE}

#### 10. Apply the OperatorGroup and Subscription objects to install the operator
> oc apply -f cp4a-operator-group.yaml<br>
> oc apply -f cp4a-operator-subscription.yaml

It takes some minutes to have the operator installed. You can see the status on OpenShift console by going to **Operators** -> **Installed Operators**.
After that you are ready to go on and create an instance of each component of Cloud Pak for Business Automation.

Check the [Cloud Pack For Business Automation](https://www.ibm.com/docs/en/cloud-paks/cp-biz-automation/22.0.2) for more information.
