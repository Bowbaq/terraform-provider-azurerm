---
layout: "azurerm"
page_title: "Azure Provider: Migrating to a renamed resource"
description: |-
    This page documents how to migrate between two resources in the Azure Provider which have been renamed.

---

# Azure Provider: Migrating to a renamed resource

Please see the following versions below to see which resources have been deprecated in favor of the renamed version. This guide covers how to migrate from the old resource to the new one.

Versions with renamed resources:
* [v1.21](#v1.21)
* [v1.37](#v1.37)

## Migrate to a renamed resource

As the Schema's for each resource are the same at this time - it's possible to migrate between the resources by updating your Terraform Configuration and updating the Statefile.

In this guide we'll assume we're migrating from the `azurerm_autoscale_setting` resource to the new `azurerm_monitor_autoscale_setting` resource, but this should be applicable for any of the resources listed below.

Assuming we have the following Terraform Configuration:

```hcl
resource "azurerm_resource_group" "example" {
  # ...
}

resource "azurerm_virtual_machine_scale_set" "example" {
  # ...
}

resource "azurerm_autoscale_setting" "example" {
  name                = "myAutoscaleSetting"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  target_resource_id  = azurerm_virtual_machine_scale_set.example.id
  # ...
}
```

We can update the Terraform Configuration to use the new resource by updating the name from `azurerm_autoscale_setting` to `azurerm_monitor_autoscale_setting`:

```hcl
resource "azurerm_resource_group" "example" {
  # ...
}

resource "azurerm_virtual_machine_scale_set" "example" {
  # ...
}

resource "azurerm_monitor_autoscale_setting" "example" {
  name                = "myAutoscaleSetting"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  target_resource_id  = azurerm_virtual_machine_scale_set.example.id
  # ...
}
```

As the Terraform Configuration has been updated - we now need to update the State. We can view the items Terraform is tracking in it's Statefile using the `terraform state list` command, for example:

```bash
$ terraform state list
azurerm_autoscale_setting.example
azurerm_resource_group.example
azurerm_virtual_machine.example
```

We can move each of the resources which has been renamed in the state using the `terraform state mv` command - for example:

```shell
$ terraform state mv azurerm_autoscale_setting.exampleazurerm_monitor_autoscale_setting.example
Moved azurerm_autoscale_setting.example to azurerm_monitor_autoscale_setting.example
```

Once this has been done, running `terraform plan` should show no changes:

```shell
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

No changes. Infrastructure is up-to-date.

This means that Terraform did not detect any differences between your
configuration and real physical resources that exist. As a result, no
actions need to be performed.
```

At this point you've switched over to using the newly renamed resources and should be able to continue using Terraform as normal.

## v1.22

In v1.22 of the AzureRM Provider several resources have been deprecated in favor of a renamed version:

| Old Name                                       | New Name                             |
| ---------------------------------------------- | ------------------------------------ |
| azurerm_log_analytics_workspace_linked_service | azurerm_log_analytics_linked_service |
| azurerm_autoscale_setting                      | azurerm_monitor_autoscale_setting    |
| azurerm_metric_alertrule                       | azurerm_monitor_metric_alert         |
| azurerm_connection_monitor                     | azurerm_network_connection_monitor   |
| azurerm_ddos_protection_plan                   | azurerm_network_ddos_protection_plan |
| azurerm_packet_capture                         | azurerm_network_packet_capture       |

## v1.37

In v1.37 of the AzureRM Provider several resources have been deprecated in favor of a renamed version:

| Old Name                                       | New Name                             |
| ---------------------------------------------- | ------------------------------------ |
| azurerm_iot_dps                                | azurerm_iothub_dps                   |
| azurerm_iot_dps_certificate                    | azurerm_iothub_dps_certificate       |
