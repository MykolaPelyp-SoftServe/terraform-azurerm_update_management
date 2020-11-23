# Terraform Azure Update Management

## Introduction

This module deploys VM update schedule and config.

## Security Controls

The following security controls can be met through configuration of this template:

* tbd

## Dependancies

Hard:

* Resource Groups
* automation account

## Usage

```terraform
terraform {
  required_version = ">= 0.12"
}

provider "azurerm" {
  skip_provider_registration = true
  version 		     = "= 2.37"
  features          {
  }
}


locals {
  update_time = "22:00"
  update_date = substr(time_offset.tomorrow.rfc3339, 0, 10)
  update_timezone = "UTC"
}

resource "time_offset" "tomorrow" {
  offset_days = 1
}

module "linux-group1-weekly-updates" {
  source                     = "./terraform-azurerm_update_management"
  name                       = "Linux-update-group1"
  resource_group_name        = "weu-rg"
  azurerm_automation_account = {name="xxx-automation"}
  operatingSystem            = "Linux"
  scope                      = ["/subscriptions/xxx"]
  patchgroup                 = "group01"
  timeZone                   = local.update_timezone
  startTime                  = "${local.update_date}T${local.update_time}:00+02:00"
  weekDays                   = ["Sunday"]
  linux_update_types         = "Critical, Security"
}


module "windows-group1-weekly-updates" {
  source                     = "./terraform-azurerm_update_management"
  name                       = "Windows-update-group1"
  resource_group_name        = "dev-weu-rg"
  azurerm_automation_account = {name="xxx-automation"}
  operatingSystem            = "Windows"
  scope                      = ["/subscriptions/xxx"]
  patchgroup                 = "group01"
  timeZone                   = local.update_timezone
  startTime                  = "${local.update_date}T${local.update_time}:00+02:00"
  weekDays                   = ["Sunday"]
  windows_update_types       = "Critical, Security, Updates"
}
```


## Variables Values

| Name                                    | Type   | Required | Value                                                                                                                                                                                                       |
| --------------------------------------- | ------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

** See variables.tf file for now for possible variables and values

## History

| Date     | Release    | Change                                                                                       |
| -------- | ---------- | -------------------------------------------------------------------------------------------- |
| 20200622 | 20200622.1 | Add support for deploy variable |
| 20200527 | 20200527.1 | 1st commit              |
