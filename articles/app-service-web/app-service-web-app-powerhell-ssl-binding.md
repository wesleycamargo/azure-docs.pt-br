<properties
	pageTitle="Associação de certificados SSL usando o PowerShell"
	description="Saiba como associar certificados SSL ao seu aplicativo Web usando o PowerShell."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# Associação de certificado SSL do Serviço de Aplicativo do Azure usando o PowerShell #

Com o lançamento do Microsoft Azure PowerShell versão 1.1.0, um novo cmdlet foi adicionado para dar ao usuário a capacidade de associar certificados SSL novos ou existentes a um aplicativo Web existente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]


## Carregando e associando um novo certificado SSL ##

Cenário: o usuário gostaria de associar um certificado SSL a um dos seus aplicativos Web.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web, o nome do aplicativo Web, o caminho de arquivo. pfx de certificado no computador do usuário, a senha para o certificado e o nome de host personalizado, poderemos usar o seguinte comando do PowerShell para criar essa associação de SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

## Carregando e associando um certificado SSL existente ##

Cenário: o usuário gostaria de associar um certificado SSL carregado anteriormente a um dos seus aplicativos Web.

Podemos obter a lista de certificados já carregados para um grupo de recursos específico usando o comando a seguir

	Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Observe que os certificados são locais para localidade e grupo de recursos específicos. O usuário precisará carregar novamente o certificado se o aplicativo Web configurado estiver em local e recurso de grupo diferentes do certificado necessário

Se soubermos o nome do grupo de recursos que contém o aplicativo Web, o nome do aplicativo Web, a impressão digital do certificado e o nome de host personalizado, poderemos usar o seguinte comando do PowerShell para criar essa associação de SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## Excluindo uma associação SSL existente  ##

Cenário: o usuário deseja excluir uma associação SSL existente.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web, o nome do aplicativo Web e o nome de host personalizado, poderemos usar o seguinte comando do PowerShell para criar essa associação de SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Observe que, se a associação de SSL removida for a última associação que usar esse certificado naquele local, por padrão o certificado será excluído. Se o usuário desejar manter o certificado, ele poderá usar a opção DeleteCertificate para mantê-lo

	Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### Referências ###
- [Introdução ao ambiente de Serviço de Aplicativo](app-service-app-service-environment-intro.md)
- [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0121_2016-->