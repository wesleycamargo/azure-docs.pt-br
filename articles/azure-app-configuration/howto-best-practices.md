---
title: Práticas recomendadas de configuração de aplicativo do Azure | Microsoft Docs
description: Saiba como usar melhor configuração de aplicativo do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413612"
---
# <a name="azure-app-configuration-best-practices"></a>Práticas recomendadas de configuração de aplicativo do Azure

Este artigo discute práticas recomendadas e padrões comuns ao usar a configuração de aplicativo do Azure.

## <a name="key-groupings"></a>Agrupamentos de chave

Configuração de aplicativo fornece duas opções para organizar as chaves: chave prefixos ou rótulos. Você pode usar um ou ambos.

Prefixos de chave são as partes de início de chaves. Logicamente, você pode agrupar um conjunto de chaves usando o mesmo prefixo em seus nomes. Prefixos podem conter vários componentes conectados por um delimitador, como `/`, semelhante a um caminho de URL, para formar um namespace. Essas hierarquias são úteis quando você está armazenando chaves para muitos aplicativos, serviços de componentes e ambientes em um repositório de configuração do aplicativo. Uma coisa importante para ter em mente é que as chaves são o que referencia o código do aplicativo para recuperar os valores das configurações correspondentes. Uma chave não deve ser alterada caso contrário, você terá que modificar o código sempre que ocorre.

Rótulos é um atributo em chaves. Eles são usados para criar variantes de uma chave. Por exemplo, você pode atribuir rótulos para várias versões de uma chave. Uma versão pode ser uma iteração, ambiente ou outras informações contextuais. O aplicativo pode solicitar um conjunto completamente diferente de valores de chave apenas especificando outro rótulo. Todas as referências de chave podem permanecer inalteradas.

## <a name="key-value-compositions"></a>Composições de chave-valor

Configuração de aplicativo trata todas as chaves armazenadas com ele como entidades independentes. Ele não tenta inferir qualquer relação entre as chaves ou herdar valores de chave com base em sua hierarquia. Você pode agregar vários conjuntos de chaves, no entanto, o uso de rótulos juntamente com a configuração apropriada de empilhamento no código do aplicativo.

Vamos examinar um exemplo. Você tem uma configuração **Asset1** cujo valor pode variar para o ambiente de "Desenvolvimento". Você pode criar uma chave chamada "Asset1" com um rótulo vazio e um chamado de "Desenvolvimento". Você coloca o valor padrão para **Asset1** no primeiro e qualquer valor específico de "Desenvolvimento" no segundo. Em seu código, primeiro recupere os valores de chave sem qualquer rótulo e, em seguida, aqueles com um rótulo de "Desenvolvimento" para substituir os valores anteriores das teclas de mesmo. Se você usar uma estrutura moderna de programação, como o .NET Core, você pode obter essa funcionalidade de empilhamento gratuitamente se você usar um provedor de configuração nativo para acessar a configuração do aplicativo. O trecho de código a seguir mostra como você pode implementar o empilhamento em um aplicativo .NET Core.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Inicialização de configuração de aplicativo

Para acessar o repositório de configuração de aplicativo, você pode usar sua cadeia de caracteres de conexão, que está disponível no portal do Azure. Cadeias de caracteres de Conexão contêm informações de credencial e são consideradas como segredos. Eles precisam ser armazenados em um cofre de chaves. Uma opção melhor é usar o Azure identidade gerenciada. Com esse método, você só precisa de URL de ponto de extremidade de configuração de aplicativo para inicializar o acesso ao seu armazenamento de configuração. Você pode inserir a URL no código do aplicativo (por exemplo, o *appSettings. JSON* arquivo). Ver [integrar com o Azure identidades gerenciadas](howto-integrate-azure-managed-service-identity.md) para obter mais detalhes.

## <a name="web-app-or-function-access-to-app-configuration"></a>Aplicativo Web ou função de acesso à configuração de aplicativo

Você pode inserir a cadeia de caracteres de conexão para o repositório de configuração de aplicativo para as configurações de aplicativo do serviço de aplicativo por meio do portal do Azure. Você também pode armazená-lo no cofre de chaves e [referenciá-lo no serviço de aplicativo](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). Você também pode usar o Azure gerenciado de identidade para acessar o repositório de configuração. Ver [integrar com o Azure identidades gerenciadas](howto-integrate-azure-managed-service-identity.md) para obter mais detalhes.

Como alternativa, você pode enviar a configuração da configuração de aplicativo no serviço de aplicativo. Configuração de aplicativo fornece uma função de exportação (no portal do Azure e CLI) que envia dados diretamente no serviço de aplicativo. Com esse método, você não precisará alterar o código do aplicativo em todos os.

## <a name="next-steps"></a>Próximas etapas

* [Chaves e valores](./concept-key-value.md)
