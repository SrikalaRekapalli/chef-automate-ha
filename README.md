# Chef-Automate-HA

## Introduction

This document explains how to deploy Chef-Automate HA template using Azure Key-Vault through Azure CLI tool.

Azure Key Vault helps safeguard cryptographic keys and secrets used by cloud applications and services. By using Key Vault, you can encrypt keys and secrets (such as authentication keys, storage account keys, data encryption keys, .PFX files, and passwords).

Chef Automate-HA environment uses key vault to store the secrets like dbpassword, replication password, cluster token, cluster name, chefdeliveryuser, chefsecrets, chefdeliverypassword.

## Chef-Automate-HA Architecture Diagram

![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/chef-automate-ha-architecture.png)



