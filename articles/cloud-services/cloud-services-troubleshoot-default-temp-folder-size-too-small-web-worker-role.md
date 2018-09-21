---
title: O tamanho da pasta TEMP padrão é muito pequeno para uma função | Microsoft Docs
description: Uma função de serviço de nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 5d60c5f2d759f3329997762270e9a0335ebbe1e9
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35764846"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>O tamanho da pasta TEMP padrão é muito pequeno em uma função de serviço de nuvem Web/trabalho
O diretório temporário padrão de uma função de serviço de nuvem Web ou trabalho tem um tamanho máximo de 100 MB, o que pode ficar completo em algum momento. Este artigo descreve como evitar a falta de espaço para o diretório temporário.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que eu fiquei sem espaço?
As variáveis de ambiente TEMP e TMP padrão do Windows estão disponíveis no código em execução em seu aplicativo. TEMP e TMP apontam para um único diretório com um tamanho máximo de 100 MB. Todos os dados armazenados nesse diretório não são persistidos durante o ciclo de vida do serviço de nuvem; se as instâncias de função em um serviço de nuvem forem recicladas, o diretório será limpo.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema
Implemente uma das alternativas a seguir:

* Configure um recurso de armazenamento local e acesse-o diretamente, em vez de usar TEMP ou TMP. Para acessar um recurso de armazenamento local no código em execução dentro de seu aplicativo, chame o método [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) .
* Configure um recurso de armazenamento local e aponte os diretórios TEMP e TMP para apontar para o caminho do recurso de armazenamento local. Essa modificação deve ser executada dentro do método [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

O exemplo de código a seguir mostra como modificar os diretórios de destino para TEMP e TMP de dentro do método OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Leia um blog que descreve [Como aumentar o tamanho da Pasta Temporária do ASP.NET da Função Web do Azure](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Confira mais [artigos sobre solução de problemas](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) para serviços de nuvem.

Para saber como solucionar os problemas das funções do serviço de nuvem usando os dados de diagnóstico do computador Azure PaaS, veja a [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
