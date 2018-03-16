---
title: "Serviço de aplicativo em uma atualização de pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre novidades da atualização para o serviço de aplicativo na pilha do Azure, os problemas conhecidos e onde baixar a atualização."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>Um notas de versão de atualização de serviço de aplicativo na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Essas notas de versão descrevem as melhorias e correções no serviço de aplicativo do Azure no Azure pilha atualização 1 e todos os problemas conhecidos. Problemas conhecidos são divididos em problemas relacionados diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização 1802 para seu sistema de pilha do Azure integradas ou implantar o kit de desenvolvimento de pilha do Azure mais recente antes de implantar o serviço de aplicativo do Azure.
>
>

## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no Azure pilha Update 1 número de compilação está **69.0.13698.9**

### <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Serviço de aplicativo do Azure na pilha do Azure, agora requer um [certificado curinga com três entidades](azure-stack-app-service-before-you-get-started.md#get-certificates) devido a melhorias da maneira na qual o SSO para Kudu agora é manipulado no serviço de aplicativo do Azure.  A nova entidade é * * *. sso.appservice.<region>. <domainname>.<extension>**
>
>

Consulte o [documentação antes de você começar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure pilha Update 1 inclui os seguintes aprimoramentos e correções:

- **Alta disponibilidade do serviço de aplicativo Azure** -1802 de pilha do Azure a cargas de trabalho de atualização habilitada para ser implantada em domínios de falha.  Portanto infraestrutura de serviço de aplicativo é capaz de ser tolerante a falhas, como ele será implantado em domínios de falha.  Por padrão todas as novas implantações de serviço de aplicativo do Azure terá esse recurso no entanto, para implantações concluídas antes do Azure pilha 1802 atualização sendo aplicada, consulte o [documentação do domínio de falha do serviço de aplicativo](azure-stack-app-service-fault-domain-update.md)

- **Implantar na rede virtual existente** -os clientes agora podem implantar o serviço de aplicativo na pilha do Azure dentro de uma rede virtual existente.  Implantando em uma rede virtual existente permite que os clientes se conectem ao SQL Server e do servidor de arquivos, necessárias para o serviço de aplicativo do Azure, sobre portas privadas.  Durante a implantação, os clientes podem selecionar para implantar em uma rede virtual existente, no entanto [devem criar sub-redes para uso pelo serviço de aplicativo](azure-stack-app-service-before-you-get-started.md#virtual-network) antes da implantação.

- Atualiza a **locatário de serviço de aplicativo, Admin, portais de funções e ferramentas de Kudu**.  Consistente com a versão do SDK do Azure pilha Portal.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
    - Adicionado **.Net Core 2.0** suporte
    - Adicionado **Node.JS** versões:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Adicionado **NPM** versões:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Adicionado **PHP** atualizações:
        - 5.6.32
        - 7.0.26 (x86 e x64)
        - 7.1.12 (x86 e x64)
    - Atualizado **Git para Windows** para v 2.14.1
    - Atualizado **Mercurial** para v4.5.0

  - Adicionado suporte para **HTTPS apenas** recurso dentro do recurso de domínio personalizado no Portal de locatário de serviço de aplicativo. 

  - Validação adicional da conexão de armazenamento no seletor de armazenamento personalizado para funções do Azure 

#### <a name="fixes"></a>Correções

- Ao criar um pacote de implantação offline, os clientes não receberão um acesso negado ao abrir a pasta do instalador do serviço de aplicativo

- Resolver problemas ao trabalhar no recurso de domínios personalizados no Portal de locatário de serviço de aplicativo.

- Impedir que os clientes que usam nomes reservados administrador durante a instalação

- Habilitada a implantação do serviço de aplicativo com **ingressado no domínio** servidor de arquivos

- Recuperação aprimorada de raiz de pilha do Azure no script de certificado e agora validar o certificado raiz no instalador do serviço de aplicativo.

- Fixado status incorreto retornado para o Azure Resource Manager quando uma assinatura é excluída que os recursos contidos no namespace Microsoft.

### <a name="known-issues-with-the-deployment-process"></a>Problemas conhecidos com o processo de implantação

- Não há nenhum problema conhecido para a implantação do serviço de aplicativo do Azure no Azure pilha atualização 1.

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Não há nenhum problema conhecido para a atualização do serviço de aplicativo do Azure no Azure pilha atualização 1.

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Não há nenhum problema conhecido para a instalação do serviço de aplicativo do Azure no Azure pilha atualização 1.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para que os administradores de nuvem operacional do serviço de aplicativo do Azure na pilha do Azure

Consulte a documentação do [Azure pilha 1802 notas de versão](azure-stack-update-1802.md)

## <a name="see-also"></a>Consulte também

- Para obter uma visão geral do serviço de aplicativo do Azure, consulte [do serviço de aplicativo do Azure na visão geral do Azure pilha](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como preparar a implantação do serviço de aplicativo na pilha do Azure, consulte [antes de iniciar o serviço de aplicativo no Azure pilha](azure-stack-app-service-before-you-get-started.md).
