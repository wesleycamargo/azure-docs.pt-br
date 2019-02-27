---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: c13ba8ab42d949e5752bfefd9b792b14a5a5ce59
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56408211"
---
## <a name="cli-shell"></a>Shell da CLI

É recomendável usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) para executar comandos da CLI. O **Cloud Shell** é um shell interativo e grátis que pode ser usado para executar as etapas deste artigo. Ferramentas comuns do Azure estão pré-instaladas e configuradas para uso com sua conta. Ele dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha. Usuários do Linux podem optar por uma experiência com o Bash, enquanto usuários do Windows podem optar pelo PowerShell.

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Logon

Para começar a usar o shell da CLI (na nuvem ou localmente), execute `az login` para criar uma conexão com o Azure.

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, será necessário abrir uma página do navegador e seguir as instruções na linha de comando para inserir um código de autorização depois de navegar até https://aka.ms/devicelogin no navegador.

### <a name="specify-location-of-files"></a>Especificar o local dos arquivos

Muitos comandos de CLI dos Serviços de Mídia permitem que você passe um parâmetro com um nome de arquivo. Se você estiver usando o **Cloud Shell**, você pode carregar seu arquivo no seu clouddrive (usando Bash ou PowerShell). 

![Carregar arquivos]

Se você estiver usando uma CLI local ou o **Cloud Shell**, você precisa especificar o caminho do arquivo de acordo com o sistema operacional ou o Cloud Shell (Bash ou PowerShell) que você está usando. Veja abaixo alguns exemplos:

Caminho relativo para a iteração (todos os sistemas operacionais)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Caminho de arquivo absoluto no sistema operacional Linux/Mac e Windows

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`


[Carregar arquivos]: ./media/media-services-cli/upload-download-files.png
