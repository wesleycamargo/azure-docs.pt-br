---
title: Matriz de módulo e versão Ansible do Azure
description: Matriz de módulo e versão Ansible do Azure
ms.service: azure
keywords: ansible, funções, matriz, versão, azure, devops
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: f2f1dccfd5b91205d0673c2b82ad7cfa673f61f6
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791810"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure
O Ansible vem com vários módulos que podem ser executados diretamente em hosts remotos ou por meio de manuais.
Este artigo lista os módulos do Ansible do Azure que pode provisionar recursos de nuvem do Azure, como a máquina virtual, rede e serviços de contêiner. Você pode obter esses módulos com a versão oficial do Ansible ou a partir das seguintes funções de manuais publicadas pela Microsoft.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Função Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Computação**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_availabilityset_facts              | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_deployment                         | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_resource                           | -            | -                           | Sim          | Sim          | Sim                                 | 
| azure_rm_resource_facts                     | -            | -                           | Sim          | Sim          | Sim                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_virtualmachineimage_facts          | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_resourcegroup                      | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_resourcegroup_facts                | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_virtualmachine                     | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_virtualmachine_extension           | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_virtualmachine_scaleset            | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_image                              |              | Sim                         | Sim          | Sim          | Sim                                 | 
| **Rede**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_virtualnetwork_facts               | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_subnet                             | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_networkinterface                   | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_networkinterface_facts             | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_publicipaddress                    | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_publicipaddress_facts              | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_dnsrecordset                       | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_dnsrecordset_facts                 | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_dnszone                            | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_dnszone_facts                      | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_loadbalancer                       | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_loadbalancer_facts                 | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Sim                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Sim                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Sim                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_securitygroup                      | Sim          | Sim                         | Sim          | Sim          | Sim                                 |
| azure_rm_route                              | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_routetable                         | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | Sim          | Sim                                 | 
| **Armazenamento**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_storageaccount_facts               | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_storageblob                        | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_managed_disk                       | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_managed_disk_facts                 | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| **Contêineres**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | Sim          | Sim          | Sim                                 | 
| azure_rm_aks_facts                          | -            | -                           | Sim          | Sim          | Sim                                 | 
| azure_rm_acs                                | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_containerinstance                  | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | Sim                                 | 
| azure_rm_containerregistry                  | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Sim                                 | 
| **Funções do Azure**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_functionapp_facts                  | Sim          | Sim                         | Sim          | Sim          | Sim                                 | 
| **Bancos de dados**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_sqlserver_facts                    | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_sqldatabase                        | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_mysqlserver                        | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_mysqldatabase                      | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_postgresqlserver                   | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_postgresqldatabase                 | -            | Sim                         | Sim          | Sim          | Sim                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Sim          | Sim                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Sim                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Sim                                 | 
| **Key Vault**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | Sim                         | Sim          | Sim          | Sim                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | Sim                               |
| azure_rm_keyvaultkey                        | -            | Sim                         | Sim          | Sim          | Sim                                 |
| azure_rm_keyvaultsecret                     | -            | Sim                         | Sim          | Sim          | Sim                                 |
| **Aplicativos Web**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | Sim          | Sim                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | Sim          | Sim                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | Sim          | Sim                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | Sim          | Sim                                 | 
| **Gerenciador de Tráfego**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | Sim          | Sim                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | Sim          | Sim                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | Sim          | Sim                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | Sim          | Sim                                 | 
| **AutoScale**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | Sim          | Sim                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | Sim          | Sim                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure
A [função do manual do azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) é a função mais completa e inclui todos os módulos do Azure mais recentes. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, recomenda-se instalar a função de guia estratégico azure_preview_module.

A função de manual azure_preview_module é liberada a cada três semanas.

## <a name="next-steps"></a>Próximas etapas
Mais informações relacionadas às funções de manual, consulte [Criar manuais reutilizáveis](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
