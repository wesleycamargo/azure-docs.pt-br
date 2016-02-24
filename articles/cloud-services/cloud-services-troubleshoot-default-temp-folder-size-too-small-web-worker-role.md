<properties
   pageTitle="O tamanho da pasta TEMP padrão é muito pequeno para uma função | Microsoft Azure"
   description="Uma função de serviço de nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# O tamanho da pasta TEMP padrão é muito pequeno em uma função de serviço de nuvem Web/trabalho

O diretório temporário padrão de uma função de serviço de nuvem Web ou trabalho tem um tamanho máximo de 100 MB, o que pode ficar completo em algum momento. Este artigo descreve como evitar a falta de espaço para o diretório temporário.

>[AZURE.NOTE] Isso se aplica somente ao uso de funções Web e de trabalho no SDK do Azure 1.0 a 1.4.

## Contate o Suporte ao Cliente do Azure

Se você precisar de mais ajuda a qualquer momento neste artigo, poderá contatar os especialistas do Azure nos [fóruns do Azure MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/).

Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para obter informações sobre como usar o Suporte do Azure, leia as [Perguntas Frequentes de Suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Por que eu fiquei sem espaço?

As variáveis de ambiente TEMP e TMP padrão do Windows estão disponíveis no código em execução em seu aplicativo. TEMP e TMP apontam para um único diretório com um tamanho máximo de 100 MB. Todos os dados armazenados nesse diretório não são persistidos durante o ciclo de vida do serviço de nuvem; se as instâncias de função em um serviço de nuvem forem recicladas, o diretório será limpo.

## Sugestão para corrigir o problema

Implemente uma das alternativas a seguir:

- Configure um recurso de armazenamento local e acesse-o diretamente, em vez de usar TEMP ou TMP. Para acessar um recurso de armazenamento local do código em execução dentro de seu aplicativo, chame o método [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Para obter mais informações sobre como configurar os recursos de armazenamento local, confira [Configurar Recursos de Armazenamento Local](cloud-services-configure-local-storage-resources.md).

- Configure um recurso de armazenamento local e aponte os diretórios TEMP e TMP para apontar para o caminho do recurso de armazenamento local. Essa modificação deve ser executada dentro do método [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

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

## Próximas etapas

Leia um blog que descreve [Como aumentar o tamanho da Pasta Temporária do ASP.NET de Função Web do Azure](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Confira mais [artigos sobre solução de problemas](..\?tag=top-support-issue&service=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas das funções do serviço de nuvem usando os dados de diagnóstico do computador Azure PaaS, veja a [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0204_2016-->