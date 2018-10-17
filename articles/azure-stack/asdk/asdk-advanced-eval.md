---
title: Tarefas de avaliação do Azure Stack avançadas | Microsoft Docs
description: Este artigo descreve tarefas avançadas de avaliação do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0b65f37310b911184c05fd8fb8554eeb824814b8
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364805"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Tarefas avançadas de avaliação do Kit de desenvolvimento do Azure Stack
Depois de você ter obtido familiaridade com os recursos e os recursos básicos do serviço do Azure Stack desenvolvimento ASDK (Kit), você pode aprofundar sua compreensão do Azure Stack ainda mais por testar os cenários mais avançados. Essas tarefas mais avançadas de avaliação são documentadas totalmente na documentação do operador do Azure Stack.

> [!NOTE]
> Embora muitas tarefas do operador têm suporte para ASDK e de produção, as implantações do Azure Stack com vários nós, nem todos os cenários de uso têm suporte para implantações de ASDK. Ver [ASDK e com vários nós do Azure Stack diferenças](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) para obter mais informações.

## <a name="delegate-offers-in-azure-stack"></a>Delegar ofertas no Azure Stack
Como o operador de pilha do Azure, você geralmente deseja colocar a outras pessoas responsável por criar ofertas e inscrever-se de que os usuários. Por exemplo, se você for um provedor de serviços, convém revendedores Inscrever clientes e gerenciá-los em seu nome. Ou, se você fizer parte de um grupo central de TI em uma empresa, convém subsidiárias para inscrever usuários sem a intervenção do usuário.

[Delegando ofertas no Azure Stack](.\.\azure-stack-delegated-provider.md) ajuda você com essas tarefas, tornando possível acessar e gerenciar mais usuários que você poderia diretamente. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Disponibilizar os bancos de dados SQL para os usuários do Azure Stack
Como um operador de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem bancos de dados SQL que eles podem usar com seus aplicativos nativos de nuvem, sites e cargas de trabalho. Fornecendo esses bancos de dados personalizados, sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos. 

Usar o adaptador de provedor de recursos do SQL Server para [disponibilizar os bancos de dados SQL para os usuários do Azure Stack](.\.\azure-stack-tutorial-sql-server.md) como um serviço do Azure Stack. Depois de instalar o provedor de recursos você conectá-lo para uma ou mais instâncias do SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Disponibilizar os aplicativos web e API aos seus usuários do Azure Stack
Como um operador de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem aplicativos de API e da web e funções do Azure. Fornecendo acesso a esses aplicativos sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos.

Implantar o provedor de recursos do serviço de aplicativo para [disponibilizar aplicativos de API e da web para seus usuários do Azure Stack](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre a oferta de serviços com sistemas integrados do Azure Stack](.\.\azure-stack-offer-services-overview.md)