---
title: Matriz de módulo e versão Ansible do Azure
description: Matriz de módulo e versão Ansible do Azure
ms.service: ansible
keywords: ansible, funções, matriz, versão, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 07/02/2018
ms.topic: article
ms.openlocfilehash: c9be94d1ea77b3609f146a373574e10b7f4d4355
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859911"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure
O Ansible vem com vários módulos que podem ser executados diretamente em hosts remotos ou por meio de manuais.
Este artigo lista os módulos do Ansible do Azure que pode provisionar recursos de nuvem do Azure, como a máquina virtual, rede e serviços de contêiner. Você pode obter esses módulos com a versão oficial do Ansible ou a partir das seguintes funções de manuais publicadas pela Microsoft.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 |  Função de manual [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------| 
| **Computação**                    |           |                          |                          |                                  | 
| azure_rm_availabilityset                    | sim          | sim                         | sim          | sim                                 | 
| azure_rm_availabilityset_facts              | sim          | sim                         | sim          | sim                                 | 
| azure_rm_deployment                         | sim          | sim                         | sim          | sim                                 | 
| azure_rm_resource                           | -            | -                           | sim          | sim                                 | 
| azure_rm_resource_facts                     | -            | -                           | sim          | sim                                 | 
| azure_rm_virtualmachine_scaleset_facts      | sim          | sim                         | sim          | sim                                 | 
| azure_rm_virtualmachineimage_facts          | sim          | sim                         | sim          | sim                                 | 
| azure_rm_resourcegroup                      | sim          | sim                         | sim          | sim                                 | 
| azure_rm_resourcegroup_facts                | sim          | sim                         | sim          | sim                                 | 
| azure_rm_virtualmachine                     | sim          | sim                         | sim          | sim                                 | 
| azure_rm_virtualmachine_extension           | sim          | sim                         | sim          | sim                                 | 
| azure_rm_virtualmachine_scaleset            | sim          | sim                         | sim          | sim                                 | 
| azure_rm_image                              |              | sim                         | sim          | sim                                 | 
| **Rede**                    |           |                          |                          |                                  | 
| azure_rm_virtualnetwork                     | sim          | sim                         | sim          | sim                                 | 
| azure_rm_virtualnetwork_facts               | sim          | sim                         | sim          | sim                                 | 
| azure_rm_subnet                             | sim          | sim                         | sim          | sim                                 | 
| azure_rm_networkinterface                   | sim          | sim                         | sim          | sim                                 | 
| azure_rm_networkinterface_facts             | sim          | sim                         | sim          | sim                                 | 
| azure_rm_publicipaddress                    | sim          | sim                         | sim          | sim                                 | 
| azure_rm_publicipaddress_facts              | sim          | sim                         | sim          | sim                                 | 
| azure_rm_dnsrecordset                       | sim          | sim                         | sim          | sim                                 | 
| azure_rm_dnsrecordset_facts                 | sim          | sim                         | sim          | sim                                 | 
| azure_rm_dnszone                            | sim          | sim                         | sim          | sim                                 | 
| azure_rm_dnszone_facts                      | sim          | sim                         | sim          | sim                                 | 
| azure_rm_loadbalancer                       | sim          | sim                         | sim          | sim                                 | 
| azure_rm_loadbalancer_facts                 | sim          | sim                         | sim          | sim                                 | 
| azure_rm_appgw                              | -            | -                           | -            | sim                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | sim                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | sim                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | sim                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | sim                                 |
| azure_rm_securitygroup                      | sim          | sim                         | sim          | sim                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | sim                                 | 
| **Armazenamento**                    |           |                          |                          |                                  | 
| azure_rm_storageaccount                     | sim          | sim                         | sim          | sim                                 | 
| azure_rm_storageaccount_facts               | sim          | sim                         | sim          | sim                                 | 
| azure_rm_storageblob                        | sim          | sim                         | sim          | sim                                 | 
| azure_rm_managed_disk                       | sim          | sim                         | sim          | sim                                 | 
| azure_rm_managed_disk_facts                 | sim          | sim                         | sim          | sim                                 | 
| **Contêineres**                    |           |                          |                          |                                  | 
| azure_rm_aks                                | -            | -                           | sim          | sim                                 | 
| azure_rm_aks_facts                          | -            | -                           | sim          | sim                                 | 
| azure_rm_acs                                | sim          | sim                         | sim          | sim                                 | 
| azure_rm_containerinstance                  | -            | sim                         | sim          | sim                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -            | sim                                 | 
| azure_rm_containerregistry                  | -            | sim                         | sim          | sim                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | sim                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | sim                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | sim                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | sim                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | sim                                 | 
| **Funções do Azure**                    |           |                          |                          |                                  | 
| azure_rm_functionapp                        | sim          | sim                         | sim          | sim                                 | 
| azure_rm_functionapp_facts                  | sim          | sim                         | sim          | sim                                 | 
| **Bancos de dados**                    |           |                          |                          |                                  | 
| azure_rm_sqlserver                          | -            | sim                         | sim          | sim                                 | 
| azure_rm_sqlserver_facts                    | -            | sim                         | sim          | sim                                 | 
| azure_rm_sqldatabase                        | -            | sim                         | sim          | sim                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | sim                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | sim                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | sim                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | sim                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | sim                                 | 
| azure_rm_mysqlserver                        | -            | sim                         | sim          | sim                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | sim                                 | 
| azure_rm_mysqldatabase                      | -            | sim                         | sim          | sim                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | sim                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | sim                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | sim                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | sim                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | sim                                 | 
| azure_rm_postgresqlserver                   | -            | sim                         | sim          | sim                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | sim                                 | 
| azure_rm_postgresqldatabase                 | -            | sim                         | sim          | sim                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | sim                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | sim                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | sim                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | sim                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | sim                                 | 
| **Key Vault**                    |           |                          |                          |                                  | 
| azure_rm_keyvault                           | -            | sim                         | sim          | sim                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -            | sim                                 |
| azure_rm_keyvaultkey                        | -            | sim                         | sim          | sim                                 |
| azure_rm_keyvaultsecret                     | -            | sim                         | sim          | sim                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure
A [função do manual do azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) é a função mais completa e inclui todos os módulos do Azure mais recentes. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, será recomendável instalar a função azure_preview_module.

A função de manual azure_preview_module é liberada a cada três semanas.

## <a name="next-steps"></a>Próximas etapas
Mais informações relacionadas às funções de manual, consulte [Criar manuais reutilizáveis](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
