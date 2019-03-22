---
title: Notas de versão 2 de atualização do serviço de aplicativo no Azure Stack | Microsoft Docs
description: Saiba mais sobre as novidades na atualização de dois para o serviço de aplicativo no Azure Stack, os problemas conhecidos e onde baixar a atualização.
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
ms.reviewer: sethm
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: d57b06a33421a94c4f849a1c1fd7cd6f1f4248dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848891"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Serviço de aplicativo em notas de versão de atualização 2 do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e correções no serviço de aplicativo do Azure no Azure Stack Update 2 e todos os problemas conhecidos. Problemas conhecidos são divididos em questões relacionadas diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização do 1804 ao seu sistema integrado do Azure Stack ou implantar o kit de desenvolvimento mais recente do Azure Stack antes de implantar o serviço de aplicativo do Azure 1.2.
>
>

## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no Azure Stack Update 2 número de build é **72.0.13698.10**

### <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Novas implantações do serviço de aplicativo do Azure no Azure Stack agora exigem uma [certificado curinga três assunto](azure-stack-app-service-before-you-get-started.md#get-certificates) devido a melhorias da maneira em que o SSO para o Kudu agora é tratado no serviço de aplicativo do Azure. É o novo assunto  **\*. sso.appservice.\< região\>.\< DomainName\>.\< extensão\>**
>
>

Consulte a [documentação antes de você começar a usar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure Stack atualização 2 inclui os seguintes aprimoramentos e correções:

- As atualizações **aplicativo de locatário de serviço, Admin, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- Atualizações para o serviço principal para melhorar a confiabilidade e mais fáceis de diagnóstico dos problemas comuns de habilitação de mensagens de erro.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
  - Adição do .NET Framework 4.7.1
  - Adicionado **Node. js** versões:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Adicionado **NPM** versões:
    - 5.6.0
  - Componentes do .NET Core atualizados para ser consistente com o serviço de aplicativo do Azure na nuvem pública.
  - Kudu atualizado

- Troca automática de implantação de slots ao recurso habilitado - [Configurando a troca automática](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- Teste no recurso de produção habilitado - [Introdução ao teste em produção](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Os Proxies de funções do Azure habilitado - [trabalhar com Proxies do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- Extensão de administração do serviço de aplicativo UX suporte adicionado para:
  - Rotação do segredo
  - Rotação de certificado
  - Rotação de credenciais do sistema
  - Rotação de cadeia de caracteres de Conexão

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Trabalhadores são incapazes de alcançar o servidor de arquivos quando o serviço de aplicativo é implantado em uma rede virtual existente e o servidor de arquivos só está disponível na rede privada.

Se você optar por implantar em uma rede virtual existente e um endereço IP interno para se conectar ao seu servidor de arquivos, você deve adicionar uma regra de segurança de saída, permitindo o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB. Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
* Origem: Qualquer
* Intervalo de porta de origem: *
* Destino: Endereços IP
* Intervalo de endereços IP de destino: Intervalo de IPs para seu servidor de arquivos
* Intervalo de porta de destino: 445
* Protocolo: TCP
* Ação: PERMITIR
* Prioridade: 700
* Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para administradores de nuvem, a operação de serviço de aplicativo do Azure no Azure Stack

Consulte a documentação no [notas de versão 1804 de pilha do Azure](azure-stack-update-1804.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço de aplicativo do Azure, consulte [serviço de aplicativo do Azure na visão geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como se preparar implantar o serviço de aplicativo no Azure Stack, consulte [antes de começar com o serviço de aplicativo no Azure Stack](azure-stack-app-service-before-you-get-started.md).
