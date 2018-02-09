---
title: Usando Ansible com o Azure
description: "Introdução ao uso do Ansible para automatizar o provisionamento de nuvem, o gerenciamento de configurações e implantações de aplicativos."
ms.service: ansible
keywords: "ansible, azure, devops, visão geral, provisionamento de nuvem, gerenciamento de configuração, implantação de aplicativo, módulos ansible, manuais do ansible"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="ansible-with-azure"></a>Ansible com o Azure

[Ansible](http://www.ansible.com) é um produto de código-fonte aberto que automatiza o provisionamento de nuvem, o gerenciamento de configurações e as implantações de aplicativos. Usando o Ansible você pode provisionar máquinas virtuais, contêineres e rede e completar infraestruturas de nuvem. Além disso, o Ansible permite automatizar a implantação e configuração de recursos em seu ambiente.

Este artigo fornece uma visão geral básica de alguns dos benefícios de usar o Ansible com o Azure.

## <a name="ansible-playbooks"></a>Manuais do Ansible

Os [manuais do Ansible](http://docs.ansible.com/ansible/latest/playbooks.html) são a linguagem de configuração, implantação e orquestração do Ansible. Eles podem descrever uma política que você deseja que seus sistemas remotos imponham ou um conjunto de etapas em um processo geral de TI. Quando você cria um manual você faz isso usando YAML, que define um modelo de uma configuração ou um processo.

## <a name="ansible-modules"></a>Módulos do Ansible

O Ansible inclui um pacote de [módulos do Ansible](http://docs.ansible.com/ansible/latest/modules_by_category.html) que pode ser executado diretamente em hosts remotos ou através dos [manuais](http://docs.ansible.com/ansible/latest/playbooks.html). Os usuários também podem criar seus próprios módulos. OS módulos podem ser usados para controlar recursos do sistema - como serviços, pacotes ou arquivos – ou executar comandos do sistema.

Para interagir com os serviços do Azure, o Ansible inclui um pacote de [módulos de nuvem do Ansible](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) que fornece as ferramentas para criar e orquestrar facilmente sua infraestrutura no Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrar cargas de trabalho existentes para o Azure

Depois que você tiver usado o Ansible para definir sua infraestrutura, você pode aplicar o manual do seu aplicativo permitindo que o Azure dimensione automaticamente seu ambiente, conforme necessário. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizar o aplicativo nativo de nuvem no Azure

O Ansible permite que você automatize aplicativos nativos de nuvem no Azure usando os microsserviços do Azure como o [Azure Functions](https://azure.microsoft.com//services/functions/) e [Kubernetes no Azure](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gerenciar implantações com inventário dinâmico
O Ansible oferece, por meio do recurso [inventário dinâmico](http://docs.ansible.com/ansible/intro_dynamic_inventory.html), a capacidade de fazer um inventário dos recursos do Azure. Você pode então marcar as implantações existentes do Azure e gerenciar as implantações marcadas através do Ansible.

## <a name="additional-azure-marketplace-options"></a>Opções adicionais do Azure Marketplace
A imagem do Azure Marketplace do [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) da Red Hat ajuda as organizações a dimensionar a automação de TI e a gerenciar implantações complexas em infraestruturas físicas, virtuais e de nuvem. O Ansible Tower inclui recursos que fornecem níveis adicionais de visibilidade, controle, segurança e eficiência necessários para as empresas de hoje. O Ansible Tower criptografa as credenciais, como chaves do Azure e SSH para que você possa delegar trabalhos para funcionários menos experientes sem o risco de expor suas credenciais.

## <a name="next-steps"></a>Próximas etapas
- [Configurar o Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Criar uma VM Linux](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)