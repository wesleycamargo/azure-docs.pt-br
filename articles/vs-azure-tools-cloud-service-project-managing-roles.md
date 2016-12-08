---
title: "Gerenciando funções nos projetos de serviços de nuvem do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como adicionar novas funções ao projeto de serviço de nuvem do Azure ou remover funções existentes usando o Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/15/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8c805f9af3154e46f25a6d24c7df33f390189340


---
# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Gerenciar funções nos projetos de serviços de nuvem do Azure com o Visual Studio
Depois de criar o projeto de serviço de nuvem do Azure, você pode adicionar novas funções a ele ou remover funções existentes. Você também pode importar um projeto existente e convertê-lo em uma função. Por exemplo, você pode importar um aplicativo Web ASP.NET e designá-lo como uma função web.

## <a name="adding-or-removing-roles"></a>Adicionando ou removendo funções
**Para adicionar uma função**

No **Gerenciador de Soluções**, abra o menu de atalho do nó **Funções** no seu projeto de serviço de nuvem e escolha **Adicionar**. Você pode selecionar uma função web existente ou uma função de trabalho da solução atual ou criar um novo projeto de função web ou de trabalho. Ou você pode selecionar um projeto apropriado, como um projeto de aplicativo Web ASP.NET e associá-lo a um projeto de função.

**Para remover uma associação de função**

No nó **Funções** do projeto de serviço de nuvem no Gerenciador de Soluções, abra o menu de atalho da função para remover e escolha **Remover**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Removendo e adicionando funções no serviço de nuvem
Se você remove uma função de seu projeto de serviço de nuvem, mas decide posteriormente adicionar a função de volta ao projeto, somente a declaração de função e os atributos básicos, como informações de diagnóstico e pontos de extremidade são adicionados. Nenhuma referência ou recursos adicionais são adicionados ao arquivo ServiceDefinition.csdef ou ServiceConfiguration.cscfg. Se você quiser adicionar essas informações, você precisará adicioná-las manualmente nesses arquivos.

Por exemplo, você pode remover uma função de serviço Web e decidir posteriormente adicionar essa função de volta em sua solução. Se você fizer isso, ocorrerá um erro. Para evitar esse erro, você deve adicionar o elemento `<LocalResources>` mostrado no seguinte XML no arquivo ServiceDefinition.csdef. Use o nome da função de serviço Web que você adicionou novamente ao projeto como parte do atributo de nome para o elemento **<LocalStorage>** . Neste exemplo, o nome da função de serviço Web é **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Próximas etapas
Saiba como configurar funções no Visual Studio lendo [Configurar as funções para um serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md),




<!--HONumber=Nov16_HO3-->


