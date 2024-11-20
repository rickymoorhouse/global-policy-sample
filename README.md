# Global Policies in API Connect

Global policies are a way to ensure a particular action is carried out for every API within a particular scope (catalog or space) in API Connect.  They can be set up as a pre or post hook for all APIs in that scope. 

[Documentation](https://www.ibm.com/docs/en/api-connect/10.0.8?topic=applications-working-global-policies)

A global policy follows a very similar format to the assembly section within your API - the simple example here will set the context variable `message.ricky.endpoint` to have a value of `https://rickymoorhouse.uk`

```yaml
global-policy: 1.0.0

info:
  name: ricky-poc-prehook
  title: Ricky POC Prehook Policy
  version: 1.0.0

gateways:
  - datapower-api-gateway

assembly:
  execute:
    - set-variable:
        version: 2.0.0
        title: set-variable
        actions:
          - set: message.ricky.endpoint
            value: https://rickymoorhouse.uk/
            type: string
```

## Upload the policy to API Connect

Once you have the policy file created (as `global-policy-prehook.yaml` in this example), you will need to upload it to API Connect:

```bash
apic global-policies:create --server {platform_api_endpoint} --catalog {catalog_name} --configured-gateway-service {gateway_service} --org {provider_org} --scope catalog global-policy-prehook.yaml

ricky-poc-prehook:1.0.0   https://platform-api/api/catalogs/4f0b2c5e-c96f-4498-93d5-80ca3d5cf4be/ce50a646-d313-4d9f-8a0d-74c28129c2cb/configured-gateway-services/2efbf9f8-6f28-470b-aa43-bf25bb6fe8de/global-policies/f87f9e76-b234-46d2-b0c9-b62b39dd731a
```

## Set the policy to be used as a prehook for the catalog

Create global-policy.yaml with the url returned in the output from the global-policies:create command.  For Example:

```yaml
global_policy_url: >-
  https://platform-api/api/catalogs/4f0b2c5e-c96f-4498-93d5-80ca3d5cf4be/ce50a646-d313-4d9f-8a0d-74c28129c2cb/configured-gateway-services/2efbf9f8-6f28-470b-aa43-bf25bb6fe8de/global-policies/f87f9e76-b234-46d2-b0c9-b62b39dd731a

```

Then you can use the following command to set this policy to be used as the prehook:

```bash
apic global-policy-prehooks:create --server {platform_api_endpoint} --catalog {catalog_name} --configured-gateway-service {gateway_service} --org {provider_org} --scope catalog global-policy.yaml
```

## To update the policy in the future

You can use the following syntax to update the policy without having to update all the apis individually:


```bash
apic global-policies:update --server {platform_api_endpoint} --catalog {catalog_name} --configured-gateway-service {gateway_service_name} --org {provider_org} --scope catalog {prehook_name}:{prehook_version} global-policy-prehook.yaml
```
