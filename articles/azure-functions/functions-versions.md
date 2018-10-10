---
title: Visão geral de versões de tempo de execução do Azure Functions
description: O Azure Functions é compatível com várias versões do tempo de execução. Aprenda as diferenças entre elas e como escolher a certa para você.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: a601ea42549abad84d6cab5c429cf94147776436
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978617"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral de versões de tempo de execução do Azure Functions

 Há duas versões principais do Azure Functions Runtime: 1.x e 2.x. A versão atual em que o novo recurso funciona e melhorias estão sendo feitas é a 2.x, embora ambas deem suporte a cenários de produção.  A seguir estão detalhadas algumas das diferenças entre as duas versões, como você pode criar cada versão e atualizar da 1.x para a 2.x.

> [!NOTE] 
> Este artigo refere-se ao serviço de nuvem Azure Functions. Para obter informações sobre o produto de versão prévia que permite que você execute o Azure Functions localmente, confira a [Visão geral do Azure Functions Runtime](functions-runtime-overview.md).

## <a name="creating-1x-apps"></a>Como criar aplicativos 1.x

Novos aplicativos criados no portal do Azure são definidos como 2.x por padrão, pois essa é a versão mais atual e na qual novos investimentos de recurso estão sendo feitos.  No entanto, você ainda pode criar aplicativos v1.x fazendo o seguinte.

1. Criar uma função do Azure no portal do Azure
1. Abra o aplicativo criado e, enquanto ele estiver em branco, abra as **Configurações de função**
1. Altere a versão de ~2 para ~1.  *Essa alternância será desabilitada se você tiver funções em seu aplicativo*.
1. Clique em Salvar e reinicie o aplicativo.  Todos os modelos agora devem criar e executar em 1.x.

## <a name="cross-platform-development"></a>Desenvolvimento multiplataforma

O tempo de execução 1.x oferece suporte a desenvolvimento e hospedagem apenas no portal ou no Windows. O tempo de execução 2.x é executado no .NET Core 2, o que significa que ele pode ser executado em todas as plataformas com suporte no .NET Core, incluindo macOS e Linux. Isso permite cenários de desenvolvimento e hospedagem multiplataforma.

## <a name="languages"></a>Linguagens

O tempo de execução 2.x usa um novo modelo de extensibilidade de linguagem. Além disso, para melhorar o desempenho e a alternância, cada aplicativo no 2.x está limitado a apenas ter funções em um único idioma. No momento, idiomas com suporte na versão 2.x são C#, F#, JavaScript e Java. Linguagens experimentais do Azure Functions 1.x não foram atualizadas para usar o novo modelo, por isso eles não são compatíveis na 2.x. A tabela a seguir indica quais linguagens de programação têm suporte em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, consulte [Linguagens com suporte](supported-languages.md).

## <a name="migrating-from-1x-to-2x"></a>Como migrar da versão 1.x para a 2.x

Você poderá desejar mover um aplicativo existente escrito em 1.x para 2.x.  A maioria das considerações necessárias na movimentação entre as versões está relacionada às alterações de tempo de execução de linguagem listadas acima (por exemplo, C# sendo movido do .NET Framework 4.7 para o .NET Core 2).  Você precisará se certificar de que seu código e bibliotecas são compatíveis com os tempos de execução de linguagem que estão sendo usados.  Certifique-se também de observar as alterações no gatilho, associações e recursos destacados abaixo.

### <a name="changes-in-triggers-and-bindings"></a>Alterações nos gatilhos e associações

Embora a maioria das propriedades de gatilho e associação e configurações permaneçam as mesmas entre as versões, na 2.x você precisará instalar alguma associação ou gatilho no aplicativo. A única exceção para isso são os gatilhos HTTP e Temporizador.  Confira [Registrar e instalar as extensões de associação](./functions-triggers-bindings.md#register-binding-extensions).  Observe que também pode haver alterações em `function.json` ou atributos de função entre as versões (por exemplo, a propriedade `connection` do CosmosDB agora é `ConnectionStringSetting`).  Confira a [tabela de associação existente](#bindings) para obter links para a documentação de cada associação.

### <a name="changes-in-features-available"></a>Alterações em recursos disponíveis

Além das alterações em linguagens e associações, há alguns recursos que foram removidos, atualizados ou substituídos entre versões.  Abaixo estão algumas das principais considerações para fazer ao começar a usar a versão 2.x depois de usar a 1.x.  As seguintes alterações foram feitas na v2.x:

* As chaves para chamar uma função sempre serão armazenadas no Armazenamento de Blobs criptografado. Na versão 1.x, por padrão, elas estavam no armazenamento de arquivos e podiam ser movidas para o blob ao habilitar recursos como slots.  Se atualizar um aplicativo da 1.x para a 2.x e os segredos estiverem no armazenamento de arquivos no momento, eles serão redefinidos.
* Para melhorar o desempenho, os gatilhos do tipo "webhook" são removidos e substituídos por gatilhos "HTTP".
* A configuração do host (`host.json`) deve ser vazia ou conter `version` de `2.0` para uma das propriedades.
* Para melhorar o monitoramento e a capacidade de observação, o painel do WebJobs (`AzureWebJobsDashboard`) é substituído pelo [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* As configurações do aplicativo (`local.settings.json`) exigem um valor para a propriedade `FUNCTIONS_WORKER_RUNTIME` que é mapeada para o idioma do aplicativo `dotnet | node | java | python`.
    * Para melhorar o tempo de inicialização e o volume, os aplicativos são limitados a uma única linguagem. Você pode publicar vários aplicativos para ter funções em diferentes linguagens para a mesma solução.
* O tempo limite padrão para as funções em um plano do Serviço de Aplicativo é de 30 minutos.  Ele pode ainda ser definido manualmente como ilimitado.
* [Devido às limitações do .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), os scripts `.fsx` para as funções de F# foram removidos. Ainda há suporte para funções F# compiladas.
* O formato dos gatilhos baseados em webhook (por exemplo, a Grade de Eventos) foi alterado para `https://{app}/runtime/webhooks/{triggerName}`

### <a name="upgrading-a-locally-developed-application"></a>Como atualizar um aplicativo desenvolvido localmente

Se o aplicativo v1.x foi desenvolvido localmente, você pode fazer alterações no aplicativo ou no projeto para torná-lo compatível com a v2.  É recomendável criar um novo aplicativo e porta sobre o código para o novo aplicativo.  Embora haja alterações que poderiam ser feitas a um aplicativo existente para executar uma atualização, há vários outros aprimoramentos entre a v1 e a v2 dos quais o código herdado provavelmente não está se beneficiando (por exemplo, em C#, a alteração de `TraceWriter` para `ILogger`).  

O caminho recomendado é iniciar com base em um dos modelos da v2 e mover o código em um novo aplicativo ou projeto.

#### <a name="visual-studio-runtime-versions"></a>Versões de tempo de execução do Visual Studio

No Visual Studio, você seleciona a versão de tempo de execução quando cria um projeto.  O Visual Studio tem os bits para ambas as versões principais e pode utilizar dinamicamente a correta para o projeto.  Essas configurações são derivadas do arquivo `.csproj`.  Para aplicativos da 1.x, o projeto tem as propriedades

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

Na v2, as propriedades do projeto são

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Clicar em Depurar ou Publicar definirá corretamente a versão adequada para as configurações do projeto.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code e Azure Functions Core Tools

Outras ferramentas locais dependem de ferramentas do Azure Functions Core Tools.  Essas ferramentas são instaladas no computador e, em geral, apenas uma versão é instalada em um computador de desenvolvimento de uma vez.  Confira as [instruções de como instalar versões específicas das ferramentas de núcleo](./functions-run-local.md).

Para o VS Code você também precisará atualizar a configuração do usuário para o `azureFunctions.projectRuntime` para coincidir com a versão das ferramentas instaladas.  Isso também atualizará os modelos e as linguagens expostos durante a criação de novos aplicativos.

### <a name="changing-version-of-apps-in-azure"></a>Como alterar a versão de aplicativos no Azure

As versões de aplicativo publicadas são definidas por meio da configuração de aplicativo `FUNCTIONS_RUNTIME_VERSION`.  Isso é definido como `~2` para aplicativos de v2 e `~1` para aplicativos de v1.  Não é recomendável alterar a versão de tempo de execução de um aplicativo que tem funções existentes publicadas sem alterar também o código dessas funções.  O caminho recomendado é criar um novo aplicativo de funções e definir para a versão apropriada, testar alterações e, em seguida, desabilitar ou excluir o aplicativo anterior.

## <a name="bindings"></a>Associações 

O tempo de execução 2.x usa um novo [modelo de extensibilidade de associação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece estas vantagens:

* Suporte para extensões de associação de terceiros.
* Desacoplamento de tempo de execução e associações. Isso permite que extensões de associação sejam lançadas independentemente e com controle de versão. Você pode, por exemplo, optar por atualizar para uma versão de uma extensão que se baseia em uma versão mais recente de um SDK subjacente.
* Um ambiente de execução mais leve, onde apenas as associações em uso são conhecidas e carregadas pelo tempo de execução.

Todas as associações do Azure Functions internas adotaram este modelo e não são mais incluídas por padrão, exceto para o gatilho Temporizador e o gatilho HTTP. Essas extensões são instaladas automaticamente quando você cria funções por meio de ferramentas como o Visual Studio ou por meio do portal.

A tabela a seguir indica quais associações têm suporte em cada versão de tempo de execução.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Codificar e testar o Azure Functions localmente](functions-run-local.md)
* [Como direcionar para versões do Azure Functions Runtime](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
