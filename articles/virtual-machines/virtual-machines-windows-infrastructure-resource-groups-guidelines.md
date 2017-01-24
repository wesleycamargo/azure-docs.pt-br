---
title: Diretrizes de grupos de recursos do Azure | Microsoft Docs
description: "Saiba mais sobre as principais diretrizes de design e implementação referentes à implantação de Grupos de Recursos em serviços de infraestrutura do Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0fbcabcd-e96d-4d71-a526-431984887451
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 1e52ae69951b6b1feee6207033a85a583d13bcc2
ms.openlocfilehash: 0d7c84d811b37a16e018f0915d3a0fde5183653f


---
# <a name="azure-resource-group-guidelines"></a>Diretrizes de grupos de recursos do Azure
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artigo destaca as noções básicas sobre como criar seu ambiente logicamente e agrupar todos os componentes em Grupos de Recursos.

## <a name="implementation-guidelines-for-resource-groups"></a>Diretrizes de implementação de Grupos de Recursos
Decisões:

* Você vai criar Grupos de Recursos pelos componentes básicos da infraestrutura ou pela implantação completa do aplicativo?
* Você precisa restringir o acesso aos Grupos de Recursos usando Controles de Acesso Baseado em Função?

Tarefas:

* Definir quais componentes principais da infraestrutura e Grupos de Recursos dedicados serão necessários.
* Examine como implementar modelos do Gerenciador de Recursos para obter implantações consistentes e reproduzíveis.
* Definir quais funções de acesso de usuário serão necessárias para controlar o acesso aos Grupos de Recursos.
* Criar o conjunto de Grupos de Recursos usando a convenção de nomenclatura. Você pode usar o Azure PowerShell ou o Portal.

## <a name="resource-groups"></a>Grupos de recursos
No Azure, é possível agrupar logicamente recursos relacionados, como contas de armazenamento, redes virtuais e VMs (máquinas virtuais) para implantar, gerenciar e mantê-las como uma única entidade. Essa abordagem facilita a implantação de aplicativos, ao mesmo tempo que reúne todos os recursos relacionados de uma perspectiva de gerenciamento ou a concessão de acesso para outros usuários a esse grupo de recursos. Para obter um entendimento mais abrangente de Grupos de Recursos, leia a [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

Um recurso importante para Grupos de Recursos é a capacidade de criar seu ambiente usando modelos. Um modelo é simplesmente um arquivo JSON que declara os recursos de armazenamento, rede e computação. Você também pode definir todas as configurações ou scripts personalizados relacionados a serem aplicados. Com esses modelos, você cria implantações consistentes e reproduzíveis para seus aplicativos. Essa abordagem facilita a criação de um ambiente em desenvolvimento e usa o mesmo modelo para criar uma implantação de produção, ou vice-versa. Para obter um melhor entendimento sobre como usar modelos, leia o [passo a passo do modelo](../resource-manager-template-walkthrough.md) que explica cada etapa de criação de um modelo.

Há duas abordagens diferentes que você pode adotar ao projetar seu ambiente com Grupos de Recursos:

* Os Grupos de Recursos para cada implantação de aplicativo que combinam as contas de armazenamento, as redes virtuais e sub-redes, as VMs, os balanceadores de carga, etc.
* Grupos de Recursos centralizados que contêm suas redes virtuais e sub-redes principais ou contas de armazenamento. Os aplicativos estão, em seguida, em seus próprios Grupos de Recursos que contêm apenas VMs, balanceadores de carga, interfaces de rede, etc.

Conforme você escala horizontalmente, criando Grupos de Recursos centralizados para sua rede virtual e subredes, facilita a criação de conexões locais cruzadas para opções de conectividade híbrida. A abordagem alternativa é cada aplicativo ter sua própria rede virtual que requer configuração e manutenção.  [Controles de Acesso Baseado em Função](../active-directory/role-based-access-control-what-is.md) fornecem uma maneira granular de controlar o acesso aos Grupos de Recursos. Para aplicativos de produção, é possível controlar os usuários que podem acessar esses recursos ou para os recursos de infraestrutura básica, você pode limitar apenas os engenheiros de infraestrutura a trabalhar com eles. Os proprietários do seu aplicativo só têm acesso aos componentes do aplicativo dentro de seu Grupo de Recursos e não o núcleo de infraestrutura do Azure do seu ambiente. Durante o projeto de seu ambiente, considere os usuários que precisam acessar os recursos e projete os Grupos de Recursos de forma condizente. 

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Dec16_HO3-->


