# Terraform Deployment

## RedHat OpenShift Provider

The RedHat OpenShift provider is required in the subscription you want to deploy this solution into. The command below will show if the provider has been registered in the subscription.

```bash
az provider show --namespace Microsoft.RedHatOpenShift -o table
```

If this returns a registration status other than `registered`, the command below needs to bue run to register it with the [`az provider`](https://docs.microsoft.com/en-us/cli/azure/provider?view=azure-cli-latest) command

```bash
az provider register --namespace 'Microsoft.RedHatOpenShift' --wait
```

## Service Principals

ARO needs a service principal to deploy. the command below will create the SP. Take note of these values.

```bash
az ad sp create-for-rbac --role Contributor --scopes /subscriptions/<sub guid> --name <service_principal> 
```

_Example Output_

```bash
Changing "<service_principal>" to a valid URI of "http://<service_principal>", which is the required format used for service principal names
Retrying role assignment creation: 1/36
Retrying role assignment creation: 2/36
Retrying role assignment creation: 3/36
Retrying role assignment creation: 4/36
{
  "appId": "8bd0d04d-0ac2-43a8-928d-705c598c6956",
  "displayName": "<service_principal>",
  "name": "http://<service_principal>",
  "password": "ac461d78-bf4b-4387-ad16-7e32e328aec6",
  "tenant": "6048c7e9-b2ad-488d-a54e-dc3f6be6a7ee"
}
```

You will also need the Service Principal object ID for the OpenShift resource provider.

```bash
az ad sp list --display-name "Azure Red Hat OpenShift RP" --query [0].objectId -o tsv
```

## Deployment

From the `deployment/terraform` directory run the following commands to deploy the environment.

```bash
terraform init

terraform apply \
  -var tenant_id="<Tenant ID> \
  -var subscription_id="<Sub ID>" \
  -var aro_sp_object_id="<SP Object ID>" \
  -var aro_sp_password="<SP Password> \
  -var aro_rp_object_id="<Ado RP Object ID>"
```