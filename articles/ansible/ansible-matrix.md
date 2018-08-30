---
title: Matriz de módulo e versão Ansible do Azure
description: Matriz de módulo e versão Ansible do Azure
ms.service: ansible
keywords: ansible, funções, matriz, versão, azure, devops
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 07/02/2018
ms.topic: article
ms.openlocfilehash: 8bbb2563991b45fe7a20ce243751c1c1a1f75b99
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094900"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure
O Ansible vem com vários módulos que podem ser executados diretamente em hosts remotos ou por meio de manuais.
Este artigo lista os módulos do Ansible do Azure que pode provisionar recursos de nuvem do Azure, como a máquina virtual, rede e serviços de contêiner. Você pode obter esses módulos com a versão oficial do Ansible ou a partir das seguintes funções de manuais publicadas pela Microsoft.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 |  Função de manual [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------| 
| **Computação**                    |           |                          |                          |                                  | 
| azure_rm_availabilityset                    | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_availabilityset_facts              | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_deployment                         | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_resource                           | -            | -                           | SIM          | SIM                                 | 
| azure_rm_resource_facts                     | -            | -                           | SIM          | SIM                                 | 
| azure_rm_virtualmachine_scaleset_facts      | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_virtualmachineimage_facts          | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_resourcegroup                      | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_resourcegroup_facts                | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_virtualmachine                     | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_virtualmachine_extension           | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_virtualmachine_scaleset            | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_image                              |              | SIM                         | sim          | SIM                                 | 
| **Rede**                    |           |                          |                          |                                  | 
| azure_rm_virtualnetwork                     | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_virtualnetwork_facts               | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_subnet                             | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_networkinterface                   | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_networkinterface_facts             | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_publicipaddress                    | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_publicipaddress_facts              | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_dnsrecordset                       | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_dnsrecordset_facts                 | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_dnszone                            | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_dnszone_facts                      | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_loadbalancer                       | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_loadbalancer_facts                 | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_appgw                              | -            | -                           | -            | SIM                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | SIM                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | SIM                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | SIM                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | SIM                                 |
| azure_rm_securitygroup                      | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | SIM                                 | 
| **Armazenamento**                    |           |                          |                          |                                  | 
| azure_rm_storageaccount                     | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_storageaccount_facts               | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_storageblob                        | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_managed_disk                       | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_managed_disk_facts                 | SIM          | sim                         | sim          | SIM                                 | 
| **Contêineres**                    |           |                          |                          |                                  | 
| azure_rm_aks                                | -            | -                           | SIM          | SIM                                 | 
| azure_rm_aks_facts                          | -            | -                           | SIM          | SIM                                 | 
| azure_rm_acs                                | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_containerinstance                  | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -            | SIM                                 | 
| azure_rm_containerregistry                  | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | SIM                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | SIM                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | SIM                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | SIM                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | SIM                                 | 
| **Funções do Azure**                    |           |                          |                          |                                  | 
| azure_rm_functionapp                        | SIM          | sim                         | sim          | SIM                                 | 
| azure_rm_functionapp_facts                  | SIM          | sim                         | sim          | SIM                                 | 
| **Bancos de dados**                    |           |                          |                          |                                  | 
| azure_rm_sqlserver                          | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_sqlserver_facts                    | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_sqldatabase                        | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | SIM                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | SIM                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | SIM                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | SIM                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | SIM                                 | 
| azure_rm_mysqlserver                        | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | SIM                                 | 
| azure_rm_mysqldatabase                      | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | SIM                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | SIM                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | SIM                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | SIM                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | SIM                                 | 
| azure_rm_postgresqlserver                   | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | SIM                                 | 
| azure_rm_postgresqldatabase                 | -            | SIM                         | sim          | SIM                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | SIM                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | SIM                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | SIM                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | SIM                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | SIM                                 | 
| **Key Vault**                    |           |                          |                          |                                  | 
| azure_rm_keyvault                           | -            | SIM                         | sim          | SIM                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -            | SIM                                 |
| azure_rm_keyvaultkey                        | -            | SIM                         | sim          | SIM                                 |
| azure_rm_keyvaultsecret                     | -            | SIM                         | sim          | SIM                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure
A [função do manual do azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) é a função mais completa e inclui todos os módulos do Azure mais recentes. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, será recomendável instalar a função azure_preview_module.

A função de manual azure_preview_module é liberada a cada três semanas.

## <a name="next-steps"></a>Próximas etapas
Mais informações relacionadas às funções de manual, consulte [Criar manuais reutilizáveis](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
