---
title: Usando o Terraform com o Azure
description: "Introdução ao uso do Terraform para controlar a versão e implantar a infraestrutura do Azure."
ms.service: virtual-machines-linux
keywords: "terraform, devops, visão geral, planejar, aplicar, automatizar"
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 61b7677b2941fe95106e43012eee458692a3cc43
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="terraform-with-azure"></a>Terraform com o Azure

O [Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de software livre para provisionar e gerenciar infraestruturas de nuvem. Ela codifica a infraestrutura em arquivos de configuração que descrevem a topologia de recursos de nuvem, como máquinas virtuais, contas de armazenamento e adaptadores de rede. A CLI (interface de linha de comando) do Terraform fornece um mecanismo simples para implantar e controlar a versão de arquivos de configuração para o Azure ou qualquer outra nuvem com suporte.

Este artigo descreve os benefícios de usar o Terraform para gerenciar a infraestrutura do Azure.

## <a name="automate-infrastructure-management"></a>Automatizar o gerenciamento de infraestrutura.

Os arquivos de configuração com base em modelo do Terraform permitem definir, provisionar e configurar recursos do Azure de forma repetível e previsível. Automatizar a infraestrutura traz várias vantagens:

- Reduz a possibilidade de erros humanos durante a implantação e gerenciamento da infraestrutura.
- Implanta o mesmo modelo várias vezes para criar ambientes de desenvolvimento, teste e produção idênticos.
- Reduz o custo de ambientes de desenvolvimento e teste ao criá-los sob demanda.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Entender as alterações de infraestrutura antes de serem aplicadas 

À medida que um recurso de topologia se torna complexo, pode ser difícil entender o significado e o impacto das alterações de infraestrutura.

O Terraform fornece uma CLI (interface de linha de comando) que permite aos usuários validar e visualizar alterações de infraestrutura antes de serem implantadas. Visualizar as alterações de infraestrutura de maneira segura e produtiva traz diversas vantagens:
- Os membros da equipe podem colaborar com mais eficiência compreendendo rapidamente as alterações propostas e seu impacto.
- Alterações inadvertidas podem ser detectadas logo no início do processo de desenvolvimento


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implantar a infraestrutura para várias nuvens

O Terraform é uma opção de ferramenta popular para cenários com várias nuvens, em que infraestruturas semelhantes são implantadas no Azure e em provedores de nuvem adicionais ou em datacenters locais. Ele permite aos desenvolvedores usar as mesmas ferramentas e arquivos de configuração para gerenciar a infraestrutura em vários provedores de nuvem.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Terraform e seus benefícios, aqui estão as próximas etapas sugeridas:

- Comece [instalando e configurando o Terraform para usá-lo no Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/terraform-install-configure).
- [Criar uma máquina virtual do Azure usando o Terraform](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/terraform-create-complete-vm)
- Explorar o [módulo do Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/) 