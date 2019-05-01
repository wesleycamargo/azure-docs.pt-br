---
title: Registrar as extensões de associação de funções do Azure
description: Saiba como registrar uma extensão de associação de funções do Azure com base em seu ambiente.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 802e177b6f3844abe4d24c26b7ea2d0d4fb1688c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697006"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrar as extensões de associação de funções do Azure

A partir da versão do Azure Functions 2.x, [associações](./functions-triggers-bindings.md) estão disponíveis como pacotes separados do tempo de execução de funções. Enquanto funções .NET acessam associações por meio de pacotes do NuGet, pacotes de extensão permitem que outro acesso de funções para todas as associações por meio de uma definição de configuração.

Considere os seguintes itens que pertencem a extensões de associação:

- As extensões de associação explicitamente não estão registradas no Functions 1.x, exceto quando [criando um C# biblioteca de classes usando o Visual Studio 2017](#local-csharp).

- Os gatilhos HTTP e timer têm suporte por padrão e não exigem uma extensão.

A tabela a seguir indica quando e como você registra associações.

| Ambiente de desenvolvimento |Registro<br/> em funções de 1. x  |Registro<br/> em funções 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático|
|Linguagens não .NET ou desenvolvimento de ferramentas básicas do Azure local|Automático|[Use as ferramentas básicas do Azure Functions e os pacotes de extensão](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|Biblioteca de classes C# usando o Visual Studio de 2017|[Usar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|[Usar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|
|Biblioteca de classes C# usando o código do Visual Studio|N/D|[Use o .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Desenvolvimento local com as ferramentas básicas do Azure Functions e os pacotes de extensão

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Biblioteca de classes C# com 2017 do Visual Studio

Em **2017 do Visual Studio**, você pode instalar os pacotes do Console do Gerenciador de pacotes usando o [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) de comando, conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome do pacote usado para uma determinada vinculação é fornecido no artigo de referência para que a associação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#packages---functions-1x).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções de tempo de execução 1. x ou 2. x são especificadas no artigo de referência para a associação.

## <a name="c-class-library-with-visual-studio-code"></a>Biblioteca de classes C# com o código do Visual Studio

Em **código do Visual Studio**, você pode instalar os pacotes do prompt de comando usando o [dotnet Adicionar pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) comando no .NET Core CLI, conforme mostrado no exemplo a seguir:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

O .NET Core CLI só pode ser usado para o desenvolvimento do Azure Functions 2.x.

O nome do pacote a ser usado para uma associação fornecida é fornecido no artigo de referência para essa associação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#packages---functions-1x).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções de tempo de execução 1. x ou 2. x são especificadas no artigo de referência para a associação.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Exemplo de gatilho e associação de função do Azure](./functions-bindings-example.md)

