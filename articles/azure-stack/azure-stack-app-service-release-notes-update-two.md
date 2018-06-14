---
title: 2 notas de versão de atualização de serviço de aplicativo na pilha do Azure | Microsoft Docs
description: Saiba mais sobre novidades na atualização de dois para o serviço de aplicativo na pilha do Azure, os problemas conhecidos e onde baixar a atualização.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360411"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Serviço de aplicativo em notas de versão de atualização 2 de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Essas notas de versão descrevem as melhorias e correções no serviço de aplicativo do Azure no Azure pilha atualização 2 e todos os problemas conhecidos. Problemas conhecidos são divididos em problemas relacionados diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização 1804 a seu sistema de pilha do Azure integradas ou implantar o kit de desenvolvimento de pilha do Azure mais recente antes de implantar 1.2 de serviço de aplicativo do Azure.
>
>

## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no Azure pilha atualização 2 número da compilação está **72.0.13698.10**

### <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Novas implantações de serviço de aplicativo do Azure na pilha do Azure agora precisam de um [certificado curinga com três entidades](azure-stack-app-service-before-you-get-started.md#get-certificates) devido a melhorias da maneira na qual o SSO para Kudu agora é manipulado no serviço de aplicativo do Azure. A nova entidade é  **\*. sso.appservice.\< região\>.\< DomainName\>.\< extensão\>**
>
>

Consulte o [documentação antes de você começar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure pilha atualização 2 inclui os seguintes aprimoramentos e correções:

- Atualiza a **locatário de serviço de aplicativo, Admin, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Azure pilha Portal.

- Atualizações para o serviço principal para melhorar a confiabilidade e permitindo um diagnóstico mais fácil problemas comuns de mensagens de erro.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
  - Adicionado .net Framework 4.7.1
  - Adicionado **Node.JS** versões:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Adicionado **NPM** versões:
    - 5.6.0
  - Atualização .net principais componentes para ser consistente com o serviço de aplicativo do Azure na nuvem pública.
  - Kudu atualizado

- Troca automática de implantação slots recurso habilitado - [Configurando a troca automática](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- Testar no recurso de produção habilitado - [Introdução ao teste em produção](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Proxies de funções do Azure habilitado - [trabalhar com Proxies de funções do Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- Extensão de administração do serviço de aplicativo UX suporte adicionado para:
  - Rotação secreta
  - Rotação de certificado
  - Rotação de credencial de sistema
  - Rotação de cadeia de caracteres de Conexão

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Os operadores são não é possível acessar o servidor de arquivos quando o serviço de aplicativo é implantado em uma rede virtual existente e o servidor de arquivos só está disponível na rede privada.

Se você optar por implantar em uma rede virtual existente e um endereço IP interno para se conectar ao seu servidor de arquivos, você deve adicionar uma regra de segurança de saída, permitindo que o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB. Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
 * Origem: qualquer
 * Intervalo de porta de origem: *
 * Destino: Endereços IP
 * Intervalo de endereço IP de destino: intervalo de endereços IP para o servidor de arquivos
 * Intervalo de porta de destino: 445
 * Protocolo: TCP
 * Ação: permitir
 * Prioridade: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para que os administradores de nuvem operacional do serviço de aplicativo do Azure na pilha do Azure

Consulte a documentação do [Azure pilha 1804 notas de versão](azure-stack-update-1804.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço de aplicativo do Azure, consulte [do serviço de aplicativo do Azure na visão geral do Azure pilha](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como preparar a implantação do serviço de aplicativo na pilha do Azure, consulte [antes de iniciar o serviço de aplicativo no Azure pilha](azure-stack-app-service-before-you-get-started.md).
