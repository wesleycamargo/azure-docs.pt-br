---
title: Executar o Azure Functions de um pacote | Microsoft Docs
description: Faça com que o Azure Functions Runtime execute suas funções montando um arquivo de pacote de implantação que contém os arquivos de projeto do aplicativo de funções.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: glenga
ms.openlocfilehash: 292b25987f183df2091667312d4e6730b7f40dda
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990893"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Executar o Azure Functions de um arquivo de pacote

> [!NOTE]
> A funcionalidade descrita neste artigo não está disponível para aplicativos de funções em execução no Linux em um [plano do serviço de aplicativo](functions-scale.md#app-service-plan).

No Azure, você pode executar suas funções diretamente de um arquivo de pacote de implantação no aplicativo de funções. A outra opção é implantar os arquivos no diretório `d:\home\site\wwwroot` do aplicativo de funções.

Este artigo descreve os benefícios de executar suas funções de um pacote. Ele também mostra como habilitar essa funcionalidade em seu aplicativo de funções.

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios da execução de um arquivo de pacote
  
Há vários benefícios na execução de um arquivo de pacote:

+ Reduz o risco de problemas de bloqueio de cópia de arquivo.
+ Pode ser implantado em um aplicativo de produção (com reinicialização).
+ Você pode ter certeza dos arquivos que estão em execução no seu aplicativo.
+ Melhora o desempenho das [implantações do Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir os tempos de inicialização a frio, particularmente para as funções de JavaScript com árvores de pacote npm grandes.

Para saber mais, veja [este comunicado](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Habilitando as funções para execução de um pacote

Para habilitar seu aplicativo de funções para execução de um pacote, basta adicionar a configuração `WEBSITE_RUN_FROM_PACKAGE` às configurações do aplicativo de funções. A configuração `WEBSITE_RUN_FROM_PACKAGE` pode ter um dos seguintes valores:

| Valor  | DESCRIÇÃO  |
|---------|---------|
| **`1`**  | Recomendado para aplicativos de função em execução no Windows. Execute de um arquivo de pacote na pasta `d:\home\data\SitePackages` do seu aplicativo de funções. Se não for [implantar implantando com o zip] (#integration-com-zip-deployment], essa opção exige que a pasta também tenha um arquivo chamado `packagename.txt`. Esse arquivo contém apenas o nome do arquivo de pacote na pasta, sem espaços em branco. |
|**`<url>`**  | Localização de um arquivo de pacote específico que você deseja executar. Ao usar o armazenamento de Blobs, você deve usar um contêiner privado com uma [SAS (Assinatura de Acesso Compartilhado)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) para habilitar o tempo de execução do Functions para acessar o pacote. Você pode usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para carregar arquivos de pacote para sua conta de armazenamento de Blobs.         |

> [!CAUTION]
> Ao executar um aplicativo de funções no Windows, a opção de URL externa gera pior desempenho de inicialização a frio. Ao implantar seu aplicativo de funções no Windows, você deve definir `WEBSITE_RUN_FROM_PACKAGE` para `1` e publique com a implantação de zip.

O exemplo a seguir mostra um aplicativo de funções configurado para ser executado de um arquivo .zip hospedado no armazenamento de Blobs do Azure:

![Configuração de aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> No momento, apenas arquivos de pacote .zip têm suporte.

## <a name="integration-with-zip-deployment"></a>Integração com a implantação do zip

[Implantação do zip][Zip deployment for Azure Functions] é um recurso do Serviço de Aplicativo do Azure que permite que você implante seu projeto de aplicativo de funções no diretório `wwwroot`. O projeto é empacotado como um arquivo de implantação .zip. As mesmas APIs podem ser usadas para implantar seu pacote na pasta `d:\home\data\SitePackages`. Com o valor de configuração de aplicativo `WEBSITE_RUN_FROM_PACKAGE` de `1`, as APIs de implantação zip copiam seu pacote para a pasta `d:\home\data\SitePackages` em vez de extrair os arquivos para `d:\home\site\wwwroot`. Ele também cria o arquivo `packagename.txt`. O aplicativo de funções é executado do pacote após a reinicialização e `wwwroot` se torna somente leitura. Para obter mais informações sobre a implantação do zip, consulte [Implantação de zip para o Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Adicionando a configuração WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
