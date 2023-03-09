# Installing Cloud Pak for Business Automation Operator - Starter Deployment

This instructions were build upon [Cloud Pack For Business Automation](https://www.ibm.com/docs/en/cloud-paks/cp-biz-automation/22.0.2) release 22.0.2 running on [OpenShift](https://docs.openshift.com/container-platform/4.12/welcome/index.html) 4.12.

ZZZ

### 1. [Instalando os operadores do CP4x](https://github.com/alexandrezanetti/cp4x/blob/main/README.md)<br>

ZZZ

It was assumed that you followed the instructions from [fyre-ocp-cp4x](https://github.com/alexandrezanetti/fyre-ocp-cp4x) repository.

Follow the steps below to install Cloud Pak for Automation Operator onto OpenShift Cluster.

The yaml files on this repository consider the following:

|Parameter|Value|
|:---------------|:----------:|
|[username]|cp4ba-user|
|[password]|password1|
|${NAMESPACE}|cp4ba|


#### 1. Move to a directory where you want to put the temp files.
```
cd /home
```

#### 2. Install the htpasswd command
```
dnf install -y httpd-tools
```

#### 3. Create a user
```
htpasswd -c -B -b users.htpasswd [username] [password]
```

#### 4. Verify that it worked.
```
htpasswd -b -v users.htpasswd [username] [password]
```

#### 5. Create a secret to contain the htpasswd file. You must be logged in as the admin user to the cluster.
```
oc create secret generic htpass-secret --from-file=htpasswd=./users.htpasswd -n openshift-config
```

#### 6. Clone this git repository
```
git clone https://github.com/alexandrezanetti/cp4ba
```

#### 7. Apply the config file with the htpasswd identity provider settings.
```
oc apply -f oauth_htpasswd.yaml
```

#### 8. Create namespace variable.
```
export NAMESPACE=cp4ba
```

#### 9. Apply the service account to the target namespace.
```
oc apply -f service-account-for-starter.yaml -n ${NAMESPACE}
```

#### 10. Bind the security context constraints (SCC) to control the actions the SA can take and what it can access.
```
oc adm policy add-scc-to-user anyuid -z ibm-cp4ba-anyuid -n ${NAMESPACE}
```

#### 11. Apply the OperatorGroup and Subscription objects to install the operator
```
oc apply -f cp4ba-operator-group.yaml
oc apply -f cp4ba-operator-subscription.yaml
```

It takes some minutes to have the operator installed. It's imperative that you confirm the operator was installed before moving forward on the next step. You can check the status on OpenShift console by going to **Operators** -> **Installed Operators** and make sure the statuses of the operators are succeed like the image down below:

<img width="1475" alt="image" src="https://user-images.githubusercontent.com/65564207/222499358-2c2a9bcf-a25e-4d74-b4a5-c267c5637cd9.png">

After that you are ready to go on and create an instance of each component of Cloud Pak for Business Automation according to the instructions available **[here](https://www.ibm.com/docs/en/cloud-paks/cp-biz-automation/22.0.2?topic=console-installing-capabilities-in-red-hat-openshift)** or follow the next steps and...

#### 12. Create CP4BA instance by applying the CP4BA starter CRD (Custom Resource Definition).
```
oc apply -f starter-create-instance.yaml
```

The instance creation takes at least 1 hour and 30 minute to finish creation. You can track the status of intallation by checking the condition of the icp4ba cartridge component:
1. Click **Operators** -> **Installed Operators** -> **IBM Automation Foundation Core**.
2. Click the **Cartridge** tab, click **icp4ba**, and then scroll to the Conditions section.

<img width="1477" alt="image" src="https://user-images.githubusercontent.com/65564207/222504834-3da965f6-d6a2-4767-b3e1-901e51ac36da.png">

**Optional:** [Validate your stater deployment](https://www.ibm.com/docs/en/cloud-paks/cp-biz-automation/22.0.2?topic=scripts-validating-your-starter-deployment)

You can check the URL for each capability by:
1. Click **Workloads** -> **ConfigMaps**.
2. Select the Namespace you created (check the ${NAMESPACE} variable - the default is cp4ba).
3. Search for the string **cp4ba-access-info**.

<img width="1479" alt="image" src="https://user-images.githubusercontent.com/65564207/222507289-b49eb788-995a-4179-b47f-8f2db40ce9cb.png">

> The contents of the ConfigMap depends on the components that are included. Each component has one or more URLs, and if needed a username and password.

For more information, check the [Cloud Pack For Business Automation](https://www.ibm.com/docs/en/cloud-paks/cp-biz-automation/22.0.2) for more information.
