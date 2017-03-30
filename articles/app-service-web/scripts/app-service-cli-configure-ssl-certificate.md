---
title: Exemplo de Script da CLI do Azure - Associar um certificado SSL personalizado a um aplicativo Web | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Associar um certificado SSL personalizado a um aplicativo Web
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 115749f29d73efdb81f491d9f31687b097f6d270
ms.lasthandoff: 03/21/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Associar um certificado SSL personalizado a um aplicativo Web

Este exemplo de script cria um aplicativo Web no Serviço de Aplicativo com seus recursos relacionados e associa o certificado SSL de um nome de domínio personalizado a ele. 

Se necessário, instale a CLI do Azure usando a instrução encontrada no [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Além disso, verifique se:

- Uma conexão com o Azure foi criado usando o comando `az login`.
- Você tem acesso à página de configuração do DNS do registrador de seu domínio.
- Você tem um arquivo .PFX válido e sua senha para o certificado SSL que você deseja carregar e associar.

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Associar um certificado SSL personalizado a um aplicativo Web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Cria um aplicativo Web do Azure. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Atualize um plano do Serviço de Aplicativo para dimensionar sua faixa de preço. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Carregar um certificado SSL a um aplicativo Web. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Associar um certificado SSL carregado a um aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).
