<properties 
   pageTitle="Como usar o comando do PowerShell do Azure para criar um contêiner de serviço de nuvem vazio" 
   description="Este artigo explica como criar o contêiner de serviço de nuvem e executar o serviço de nuvem relacionado a operações de gerenciamento usando o script do PowerShell" 
   services="cloud-services" 
   documentationCenter=".net" 
   authors="cawaMS" 
   manager="" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="cawa"/>

# Como usar o comando do PowerShell do Azure para criar um contêiner de serviço de nuvem vazio
1. Instalar o cmdlet do PowerShell do Microsoft Azure em [baixar o PowerShell do Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409). Para obter mais instruções sobre a instalação do cmdlet do PowerShell do Azure e conectar-se à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Azure](install-configure-powershell.md).

2. **New-AzureService** é o cmdlet para criar um contêiner de serviço de nuvem vazio.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>] 
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Um exemplo de chamar o cmdlet é: 
```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Para obter mais informações sobre como criar um serviço de nuvem do Azure, execute: 
```
Get-help New-AzureService
```

4. Próximas etapas:

   - Para gerenciar a implantação do serviço de nuvem, consulte os comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) e [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Também consulte [Como configurar serviços de nuvem](cloud-services-how-to-configure.md)

    - Para publicar seu projeto de serviço de nuvem do Azure, consulte o exemplo de código **PublishCloudService.ps1** em [Fornecimento contínuo de serviço de nuvem no Azure](cloud-services-dotnet-continuous-delivery.md)


    


<!--HONumber=52-->