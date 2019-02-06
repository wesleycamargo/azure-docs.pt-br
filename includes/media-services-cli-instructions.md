---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104976"
---
## <a name="open-cli-shell"></a>Abrir o shell da CLI

É recomendável usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) para executar comandos da CLI. O **Cloud Shell** é um shell interativo e grátis que pode ser usado para executar as etapas deste artigo. Ferramentas comuns do Azure estão pré-instaladas e configuradas para uso com sua conta. Basta selecionar o botão Copiar para copiar o código, colá-lo no Cloud Shell e pressionar Enter para executá-lo. Há algumas maneiras de abrir o Cloud Shell:

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Logon

Para começar a usar o shell da CLI (na nuvem ou localmente), execute `az login` para criar uma conexão com o Azure.

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, será necessário abrir uma página do navegador e seguir as instruções na linha de comando para inserir um código de autorização depois de navegar até https://aka.ms/devicelogin no navegador.

### <a name="specify-location-of-files"></a>Especificar o local dos arquivos

Muitos comandos de CLI dos Serviços de Mídia permitem que você passe um parâmetro com um nome de arquivo. 

Se estiver usando o **Azure Cloud Shell**, carregue seu arquivo no **Azure Cloud Shell**. O botão de upload/download de arquivos está na parte superior da janela do shell. Em seguida, faça uma referência ao arquivo desta forma: `@{FileName}.` 

![Carregar arquivos]

Se estiver usando a CLI do Azure localmente, especifique o caminho completo do arquivo. Por exemplo, `@c:\tracks.json`.


[Carregar arquivos]: ./media/media-services-cli/upload-download-files.png
