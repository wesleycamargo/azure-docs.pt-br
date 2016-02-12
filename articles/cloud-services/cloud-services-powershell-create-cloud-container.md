<properties
   pageTitle="Como usar o comando do Azure PowerShell para criar um contêiner de serviço de nuvem vazio | Microsoft Azure"
   description="Este artigo explica como criar o contêiner de serviço de nuvem e executar o serviço de nuvem relacionado a operações de gerenciamento usando um script do PowerShell"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="paulyuk"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="01/13/2015"
   ms.author="cawa"/>

# Usar um comando do Azure PowerShell para criar um contêiner de serviço de nuvem vazio
Este artigo explica como criar rapidamente um contêiner de Serviços de Nuvem usando cmdlets do Azure PowerShell. Siga as etapas abaixo:

1. Instale o cmdlet do Microsoft Azure PowerShell da página [Baixar o Azure PowerShell](http://aka.ms/webpi-azps).
2. Abra o prompt de comando do PowerShell.
3. Use [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para entrar.

    > [AZURE.NOTE] Para obter mais instruções sobre a instalação do cmdlet do Azure PowerShell e conectar-se à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

4. Use **New-AzureService** para criar um contêiner de serviço de nuvem do Azure vazio.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Siga este exemplo para invocar o cmdlet: ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

Para saber mais sobre como criar o Serviço de Nuvem do Azure, execute: ```
Get-help New-AzureService
```

### Próximas etapas

 * Para gerenciar a implantação do serviço de nuvem, consulte os comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) e [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Você também pode consultar [Como configurar os serviços de nuvem](cloud-services-how-to-configure.md) para saber mais.

 * Para publicar seu projeto de serviço de nuvem no Azure, consulte o exemplo de código **PublishCloudService.ps1** em [Fornecimento contínuo de serviço de nuvem no Azure](cloud-services-dotnet-continuous-delivery.md).

<!---HONumber=AcomDC_0204_2016-->