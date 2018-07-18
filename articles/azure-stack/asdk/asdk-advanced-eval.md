---
title: Tarefas de avaliação do Azure pilha avançadas | Microsoft Docs
description: Este artigo descreve tarefas avançadas de avaliação de pilha do Azure.
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
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
ms.locfileid: "29976348"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Tarefas avançadas de avaliação do Kit de desenvolvimento de pilha do Azure
Depois de ter obtido familiaridade com os recursos e os recursos de serviço básico do Kit de desenvolvimento na pilha do Azure (ASDK), você pode aprofundar o seu conhecimento da pilha do Azure ainda mais por testar os cenários mais avançados. Essas tarefas de avaliação mais avançadas são documentadas totalmente na documentação do operador de pilha do Azure.

> [!NOTE]
> Embora muitas tarefas de operador são compatíveis com ASDK e produção, as implantações do Azure pilha com vários nós, nem todos os cenários de uso têm suporte para implantações de ASDK. Consulte [ASDK e vários nós do Azure pilha diferenças](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) para obter mais informações.

## <a name="delegate-offers-in-azure-stack"></a>Delegar ofertas no Azure Stack
Como o operador de pilha do Azure, você geralmente deseja colocar outras pessoas responsável por criar ofertas e usuários de assinatura. Por exemplo, se você for um provedor de serviço, convém revendedores Inscrever clientes e gerenciá-los em seu nome. Ou, se você é membro de um grupo central de TI em uma empresa, convém subsidiárias para inscrever-se os usuários sem a intervenção do usuário.

[Delegando ofertas na pilha do Azure](.\.\azure-stack-delegated-provider.md) ajuda com essas tarefas, tornando possível acessar e gerenciar mais usuários que você pode diretamente. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Disponibilizar os bancos de dados SQL para os usuários de pilha do Azure
Como um operador de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem bancos de dados SQL que podem ser usadas com seus aplicativos de nuvem nativo, sites e cargas de trabalho. Fornecendo esses bancos de dados personalizados, sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos. 

Usar o adaptador de provedor de recursos do SQL Server para [disponibilizar os bancos de dados SQL para os usuários do Azure pilha](.\.\azure-stack-tutorial-sql-server.md) como um serviço de pilha do Azure. Depois de instalar o provedor de recursos conectá-lo para uma ou mais instâncias do SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tornar disponível para os usuários do Azure pilha da web e aplicativos de API
Como um operador de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem aplicativos web e funções do Azure e de API. Fornecendo acesso a esses aplicativos sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos.

Implantar o provedor de recursos do serviço de aplicativo [disponibilizar os aplicativos web e API para os usuários de pilha do Azure](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre a oferta de serviços com os sistemas de pilha do Azure integrado](.\.\azure-stack-offer-services-overview.md)