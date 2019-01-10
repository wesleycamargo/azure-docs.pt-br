---
title: Matriz de módulo e versão Ansible do Azure
description: Matriz de módulo e versão Ansible do Azure
ms.service: ansible
keywords: ansible, funções, matriz, versão, azure, devops
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: 5265b6f6ebf779c83792ab2569c1b613d11070da
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051519"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure
O Ansible vem com vários módulos que podem ser executados diretamente em hosts remotos ou por meio de manuais.
Este artigo lista os módulos do Ansible do Azure que pode provisionar recursos de nuvem do Azure, como a máquina virtual, rede e serviços de contêiner. Você pode obter esses módulos com a versão oficial do Ansible ou a partir das seguintes funções de manuais publicadas pela Microsoft.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | [Função Ansible](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Computação**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_availabilityset_facts              | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_deployment                         | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_resource                           | -            | -                           | SIM          | sim          | SIM                                 | 
| azure_rm_resource_facts                     | -            | -                           | SIM          | sim          | SIM                                 | 
| azure_rm_virtualmachine_scaleset_facts      | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_virtualmachineimage_facts          | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_resourcegroup                      | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_resourcegroup_facts                | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_virtualmachine                     | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_virtualmachine_extension           | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_virtualmachine_scaleset            | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_image                              |              | SIM                         | sim          | sim          | SIM                                 | 
| **Rede**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_virtualnetwork_facts               | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_subnet                             | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_networkinterface                   | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_networkinterface_facts             | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_publicipaddress                    | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_publicipaddress_facts              | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_dnsrecordset                       | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_dnsrecordset_facts                 | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_dnszone                            | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_dnszone_facts                      | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_loadbalancer                       | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_loadbalancer_facts                 | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | SIM                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | SIM                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | SIM                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_securitygroup                      | SIM          | sim                         | sim          | sim          | SIM                                 |
| azure_rm_route                              | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_routetable                         | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | SIM          | SIM                                 | 
| **Armazenamento**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_storageaccount_facts               | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_storageblob                        | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_managed_disk                       | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_managed_disk_facts                 | SIM          | sim                         | sim          | sim          | SIM                                 | 
| **Contêineres**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | SIM          | sim          | SIM                                 | 
| azure_rm_aks_facts                          | -            | -                           | SIM          | sim          | SIM                                 | 
| azure_rm_acs                                | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_containerinstance                  | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | SIM                                 | 
| azure_rm_containerregistry                  | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | SIM                                 | 
| **Funções do Azure**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | SIM          | sim                         | sim          | sim          | SIM                                 | 
| azure_rm_functionapp_facts                  | SIM          | sim                         | sim          | sim          | SIM                                 | 
| **Bancos de dados**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_sqlserver_facts                    | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_sqldatabase                        | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_mysqlserver                        | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_mysqldatabase                      | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_postgresqlserver                   | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_postgresqldatabase                 | -            | SIM                         | sim          | sim          | SIM                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | SIM          | SIM                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | SIM                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | SIM                                 | 
| **Key Vault**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | SIM                         | sim          | sim          | SIM                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | SIM                               |
| azure_rm_keyvaultkey                        | -            | SIM                         | sim          | sim          | SIM                                 |
| azure_rm_keyvaultsecret                     | -            | SIM                         | sim          | sim          | SIM                                 |
| **Aplicativos Web**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | SIM          | SIM                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | SIM          | SIM                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | SIM          | SIM                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | SIM          | SIM                                 | 
| **Gerenciador de Tráfego**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | SIM          | SIM                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | SIM          | SIM                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | SIM          | SIM                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | SIM          | SIM                                 | 
| **AutoScale**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | SIM          | SIM                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | SIM          | SIM                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure
A [função do manual do azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) é a função mais completa e inclui todos os módulos do Azure mais recentes. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, recomenda-se instalar a função de guia estratégico azure_preview_module.

A função de manual azure_preview_module é liberada a cada três semanas.

## <a name="next-steps"></a>Próximas etapas
Mais informações relacionadas às funções de manual, consulte [Criar manuais reutilizáveis](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
