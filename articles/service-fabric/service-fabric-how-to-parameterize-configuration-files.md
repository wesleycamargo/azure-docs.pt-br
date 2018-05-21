---
title: Como parametrizar os arquivos de configuração no Azure Service Fabric | Microsoft Docs
description: Mostra como parametrizar os arquivos de configuração no Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: e5bb2f270cc5a6f288e1e995f4bfa74f4e3551b7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Como parametrizar os arquivos de configuração no Service Fabric

Este artigo mostra como parametrizar os arquivos de configuração no Service Fabric.

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimento para parametrizar arquivos de configuração

Neste exemplo, você deve substituir um valor de configuração usando os parâmetros na implantação do aplicativo.

1. Abra o arquivo Config\Settings.xml.
1. Defina um parâmetro de configuração adicionando o seguinte XML:

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Salve e feche o arquivo.
1. Abra o arquivo `ApplicationManifest.xml` .
1. Adicione um elemento `ConfigOverride` fazendo referência ao pacote de configuração, à seção e ao parâmetro.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Ainda no arquivo ApplicationManifest.xml, especifique o parâmetro no elemento `Parameters`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. E defina um `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> No caso em que você adicionar um ConfigOverride, o Service Fabric sempre escolherá os parâmetros do aplicativo ou o valor padrão especificado no manifesto do aplicativo.
>
>

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre alguns dos principais conceitos que são discutidos neste artigo, veja os artigos sobre [Gerenciar aplicativos de vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

Para obter informações sobre outras funcionalidades de gerenciamento de aplicativo disponíveis no Visual Studio, confira [Gerenciar seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).