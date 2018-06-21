---
title: Diretrizes sobre a implantação de aplicativos Web do Azure usando modelos | Microsoft Docs
description: Recomendações para criar modelos do Azure Resource Manager para implantar aplicativos Web.
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: 8c29cf5a65e9587b281a6000b5b4eff47f11da91
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807315"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Diretrizes sobre a implantação de aplicativos Web usando modelos do Azure Resource Manager

Este artigo fornece recomendações para a criação de modelos do Azure Resource Manager para implantar soluções do Serviço de Aplicativo do Azure. Essas recomendações podem ajudá-lo a evitar problemas comuns.

## <a name="define-dependencies"></a>Definir dependências

Definir dependências para aplicativos web requer uma compreensão de como os recursos de um aplicativo web interagem. Se você especificar dependências em uma ordem incorreta, poderá causar erros de implantação ou criar uma condição de corrida que interrompe a implantação.

> [!WARNING]
> Se você incluir uma extensão de site do MSDeploy no seu modelo, será necessário configurar qualquer recurso de configuração como dependente do recurso do MSDeploy. As alterações de configuração fazem com que o site seja reiniciado de maneira assíncrona. Ao tornar os recursos de configuração dependentes do MSDeploy, você assegura que o MSDeploy seja concluída antes do reinício do site. Sem essas dependências, o site poderá reiniciar durante o processo de implantação do MSDeploy. Para um modelo de exemplo, consulte [Modelo Wordpress com dependência de Implantação da Web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A imagem a seguir mostra a ordem de dependência para vários recursos do Serviço de Aplicativo:

![Dependências do aplicativo Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Os recursos são implantados na seguinte ordem:

**Camada 1**
* Plano do Serviço de Aplicativo.
* Quaisquer outros recursos relacionados, como bancos de dados ou contas de armazenamento.

**Camada 2**
* Aplicativo Web - depende do plano do Serviço de Aplicativo.
* Instância do Application Insights que direciona o farm de servidores - depende do plano do Serviço de Aplicativo.

**Camada 3**
* Controle do código-fonte - depende do aplicativo Web.
* Extensão de site do MSDeploy - depende do aplicativo Web.
* Instância do Application Insights que direciona o farm de servidores - depende do aplicativo Web.

**Camada 4**
* Certificado do Serviço de Aplicativo - depende do controle do código-fonte ou MSDeploy se qualquer um estiver presente. Caso contrário, ele depende do aplicativo web.
* Conjunto de configurações (cadeias de conexão, valores de configuração da web, configurações de aplicativo) - depende do controle do código-fonte ou MSDeploy se qualquer um estiver presente. Caso contrário, ele depende do aplicativo web.

**Camada 5**
* Associações de nome do host - depende do certificado se estiver presente. Caso contrário, ele depende de um recurso de nível superior.
* Extensões de site - depende das configurações, se presente. Caso contrário, ele depende de um recurso de nível superior.

Normalmente, sua solução inclui apenas alguns desses recursos e camadas. Para camadas ausentes, mapeie recursos mais baixos para o próximo nível superior.

O exemplo a seguir mostra parte de um modelo. O valor de configuração da cadeia de conexão depende da extensão do MSDeploy. A extensão do MSDeploy depende do aplicativo Web e do banco de dados. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Para um exemplo pronto para execução que usa o código acima, consulte [Modelo: Compilar um aplicativo Web Umbraco simples](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Localizar informações sobre erros do MSDeploy

Se seu modelo do Resource Manager usar MSDeploy, as mensagens de erro de implantação poderão ser difíceis de compreender. Para obter mais informações após uma implantação com falha, experimente as etapas a seguir:

1. Vá para o [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) do site.
2. Navegue até a pasta em D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Procure os arquivos appManagerStatus.xml e appManagerLog.xml. O primeiro arquivo registra o status. O segundo arquivo registra as informações sobre o erro. Se o erro não estiver claro, você poderá incluí-lo ao solicitar ajuda no fórum.

## <a name="choose-a-unique-web-app-name"></a>Escolha um nome de aplicativo Web exclusivo

O nome do seu aplicativo Web deve ser globalmente exclusivo. Você pode usar uma convenção de nomenclatura que provavelmente seja exclusiva ou pode usar a [função uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) para ajudar a gerar um nome exclusivo.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>Próximas etapas

* Para um tutorial sobre a implantação de aplicativos Web com um modelo, consulte [Provisionar e implantar microsserviços previsíveis no Azure](app-service-deploy-complex-application-predictably.md).