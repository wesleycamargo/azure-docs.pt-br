---
services: virtual-machines
title: "Configuração da CLI do Azure para gerenciamento de serviços"
author: squillace
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machine
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: linux
ms.workload: infrastructure
ms.date: 04/13/2015
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 737e4be21eefcbd6fbd31d15a6f77770cd59ca67
ms.lasthandoff: 03/21/2017


---
## <a name="using-azure-cli"></a>Usando a CLI do Azure
As etapas a seguir o ajudarão a usar a CLI do Azure facilmente com a versão mais recente e com a assinatura correta. Se você precisar instalar a CLI do Azure e conectá-la primeiro em sua conta, confira [Interface de Linha de Comando do Azure (CLI do Azure)](../articles/cli-install-nodejs.md).

### <a name="step-1-update-azure-cli-version"></a>Etapa 1: atualizar a versão da CLI do Azure
Para usar a CLI do Azure para comandos imperativos com modo de gerenciamento de serviço, você deve ter uma versão recente, se possível. Para verificar a sua versão, digite `azure --version`. Você deve ver algo como:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se quiser atualizar a sua versão da CLI do Azure, confira [CLI do Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Etapa 2: definir a conta e assinatura do Azure
Depois de conectar a CLI do Azure com a conta que você deseja usar, você pode ter mais de uma assinatura. Se você fizer isso, deverá examinar as assinaturas disponíveis para sua conta ao digitar `azure account list`, onde `azure account set <subscription id or name> true`nome ou id de assinatura*é a id da assinatura ou o nome da assinatura* com que você gostaria de trabalhar na sessão atual. Você verá algo semelhante ao que se segue:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [!NOTE]
> Se você ainda não tiver uma conta do Azure, mas tem uma assinatura para a assinatura do MSDN, você pode obter créditos gratuitos do Azure ativando os seus [benefícios de assinante do MSDN aqui](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – ou você pode usar a conta gratuita. Ambos funcionarão para o acesso ao Azure.
> 
> 


