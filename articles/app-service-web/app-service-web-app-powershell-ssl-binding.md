---
title: "Associação de certificados SSL usando o PowerShell"
description: Saiba como associar certificados SSL ao seu aplicativo Web usando o PowerShell.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 8ce32508-e982-48d3-b878-0e526afda537
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: ahmedelnably
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e0302c4e4e63383a2b19bd6f02f3be8d83f5ff98


---
# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Associação de certificado SSL do Serviço de Aplicativo do Azure usando o PowerShell
Com o lançamento do Microsoft Azure PowerShell versão 1.1.0, um novo cmdlet foi adicionado para dar ao usuário a capacidade de associar certificados SSL novos ou existentes a um aplicativo Web existente.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Para saber mais sobre como usar os cmdlets do Azure PowerShell baseados no Azure Resource Manager para gerenciar Aplicativos Web, confira [Azure Resource Manager based PowerShell commands for Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Carregando e associando um novo certificado SSL
Cenário: o usuário gostaria de associar um certificado SSL a um dos seus aplicativos Web.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web, o nome do aplicativo Web, o caminho de arquivo. pfx de certificado no computador do usuário, a senha para o certificado e o nome de host personalizado, poderemos usar o seguinte comando do PowerShell para criar essa associação de SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Observe que antes de adicionar uma associação SSL a um aplicativo Web, você já deverá ter configurado um nome de host (domínio personalizado). Se o nome de host não estiver configurado, você receberá um erro dizendo que o ‘nome de host’ não existe durante a execução de New-AzureRmWebAppSSLBinding. Você pode adicionar um nome de host diretamente do portal ou usando o Azure PowerShell. O seguinte trecho do PowerShell pode ser usado para configurar o nome do host antes de executar o New-AzureRmWebAppSSLBinding.   

    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   

É importante entender que o cmdlet Set-AzureRmWebApp substitui o nome de host do aplicativo Web. Portanto, o trecho do PowerShell acima é anexado à lista existente de nomes de host para o aplicativo Web.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Carregando e associando um certificado SSL existente
Cenário: o usuário gostaria de associar um certificado SSL carregado anteriormente a um dos seus aplicativos Web.

Podemos obter a lista de certificados já carregados para um grupo de recursos específico usando o comando a seguir

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Observe que os certificados são locais para localidade e grupo de recursos específicos. O usuário precisará carregar novamente o certificado se o aplicativo Web configurado estiver em local e recurso de grupo diferentes do certificado necessário 

Se soubermos o nome do grupo de recursos que contém o aplicativo Web, o nome do aplicativo Web, a impressão digital do certificado e o nome de host personalizado, poderemos usar o seguinte comando do PowerShell para criar essa associação de SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Excluindo uma associação SSL existente
Cenário: o usuário deseja excluir uma associação SSL existente.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web, o nome do aplicativo Web e o nome de host personalizado, poderemos usar o seguinte comando do PowerShell para criar essa associação de SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Observe que, se a associação de SSL removida for a última associação que usar esse certificado naquele local, por padrão o certificado será excluído. Se o usuário desejar manter o certificado, ele poderá usar a opção DeleteCertificate para mantê-lo

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Referências
* [Azure Resource Manager based PowerShell commands for Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
* [Introdução ao ambiente de Serviço de Aplicativo](app-service-app-service-environment-intro.md)
* [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md)




<!--HONumber=Nov16_HO3-->


