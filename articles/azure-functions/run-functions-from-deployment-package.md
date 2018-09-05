---
title: Executar o Azure Functions de um pacote | Microsoft Docs
description: Faça com que o Azure Functions Runtime execute suas funções montando um arquivo de pacote de implantação que contém os arquivos de projeto do aplicativo de funções.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 273b1a84b5ebe5f5751a05ef24e4721e7226bca6
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819505"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Executar o Azure Functions de um arquivo de pacote

> [!NOTE]
> A funcionalidade descrita neste artigo está atualmente em versão prévia, ela não está disponível para o Functions no Linux.

No Azure, você pode executar suas funções diretamente de um arquivo de pacote de implantação no aplicativo de funções. A outra opção é implantar os arquivos de projeto de função no diretório `d:\home\site\wwwroot` do aplicativo de funções.

Este artigo descreve os benefícios de executar suas funções de um pacote. Ele também mostra como habilitar essa funcionalidade em seu aplicativo de funções.

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios da execução de um arquivo de pacote
  
Há vários benefícios na execução de um arquivo de pacote:

+ Reduz o risco de problemas de bloqueio de cópia de arquivo.
+ Pode ser implantado em um aplicativo de produção (com reinicialização).
+ Você pode ter certeza dos arquivos que estão em execução no seu aplicativo.
+ Melhora o desempenho das [implantações do Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir o tempo de inicialização a frio de funções do JavaScript.

Para saber mais, veja [este comunicado](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Habilitando as funções para execução de um pacote

Para habilitar seu aplicativo de funções para execução de um pacote, basta adicionar a configuração `WEBSITE_RUN_FROM_ZIP` às configurações do aplicativo de funções. A configuração `WEBSITE_RUN_FROM_ZIP` pode ter um dos seguintes valores:

| Valor  | DESCRIÇÃO  |
|---------|---------|
|**`<url>`**  | Localização de um arquivo de pacote específico que você deseja executar. Ao usar o armazenamento de Blobs, você deve usar um contêiner privado com uma [SAS (Assinatura de Acesso Compartilhado)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) para habilitar o tempo de execução do Functions para acessar o pacote. Você pode usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para carregar arquivos de pacote para sua conta de armazenamento de Blobs.         |
| **`1`**  | Execute de um arquivo de pacote na pasta `d:\home\data\SitePackages` do seu aplicativo de funções. Essa opção exige que a pasta também tenha um arquivo chamado `packagename.txt`. Esse arquivo contém apenas o nome do arquivo de pacote na pasta, sem espaços em branco. |

O exemplo a seguir mostra um aplicativo de funções configurado para ser executado de um arquivo .zip hospedado no armazenamento de Blobs do Azure:

![Configuração de aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> No momento, apenas arquivos de pacote .zip têm suporte.

## <a name="integration-with-zip-deployment"></a>Integração com a implantação do zip

[Implantação do zip][Zip deployment for Azure Functions] é um recurso do Serviço de Aplicativo do Azure que permite que você implante seu projeto de aplicativo de funções no diretório `wwwroot`. O projeto é empacotado como um arquivo de implantação .zip. As mesmas APIs podem ser usadas para implantar seu pacote na pasta `d:\home\data\SitePackages`. Com o valor de configuração de aplicativo `WEBSITE_RUN_FROM_ZIP` de `1`, as APIs de implantação zip copiam seu pacote para a pasta `d:\home\data\SitePackages` em vez de extrair os arquivos para `d:\home\site\wwwroot`. Ele também cria o arquivo `packagename.txt`. O aplicativo de funções é executado do pacote após a reinicialização e `wwwroot` se torna somente leitura. Para obter mais informações sobre a implantação do zip, consulte [Implantação de zip para o Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfromzip-setting"></a>Adicionando a configuração WEBSITE_RUN_FROM_ZIP

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md