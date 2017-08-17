---
title: Adicionar um nome personalizado e SSL a um aplicativo Web do Azure| Microsoft Docs
description: "Saiba como preparar seu aplicativo Web do Azure para entrar em produção adicionando a marca da sua empresa. Mapeie o nome de domínio personalizado para seu aplicativo Web e proteja-o com um certificado SSL personalizado."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/29/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017

---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>Adicionar um nome personalizado e SSL a um aplicativo Web do Azure

Este tutorial mostra como mapear rapidamente um nome de domínio personalizado para seu aplicativo Web do Azure e, depois, protegê-lo com um certificado SSL personalizado. 

## <a name="before-you-begin"></a>Antes de começar

Antes de executar este exemplo, instale a [CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) localmente.

Também é necessário acesso administrativo à página de configuração de DNS para seu respectivo provedor de domínio. Por exemplo, para adicionar `www.contoso.com`, você precisa ser capaz de configurar entradas DNS para `contoso.com`.

Por fim, você precisa de um arquivo .PFX válido _e_ de sua senha para o certificado SSL que você deseja carregar e associar. Esse certificado SSL deve ser configurado para proteger o nome de domínio personalizado que você deseja. No exemplo acima, seu certificado SSL deve proteger `www.contoso.com`. 

## <a name="step-1---create-an-azure-web-app"></a>Etapa 1 - Criar um aplicativo Web do Azure

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Agora, usaremos a CLI 2.0 do Azure em uma janela do terminal para criar os recursos necessários para hospedar nosso aplicativo Node.js no Azure.  Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Criar um grupo de recursos   
Crie um grupo de recursos com [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure, como os aplicativos Web, bancos de dados e contas de armazenamento, são implantados e gerenciados. 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

Para ver quais são os valores possíveis para `---location`, use o comando `az appservice list-locations` da CLI do Azure.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Criar um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

O exemplo a seguir cria um Plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço **Basic**.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir. 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Agora que um Plano do Serviço de Aplicativo foi criado, crie um Aplicativo Web no Plano do Serviço de Aplicativo `myAppServicePlan`. O aplicativo Web fornece um espaço de hospedagem para implantar seu código, bem como uma URL para exibir o aplicativo implantado. Use o comando [az appservice web create](/cli/azure/appservice/web#create) para criar o Aplicativo Web. 

No comando abaixo, substitua o espaço reservado `<app_name>` por seu próprio nome exclusivo de aplicativo. O nome exclusivo será usado como a parte do nome de domínio padrão para o aplicativo Web, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Posteriormente, você poderá mapear qualquer entrada DNS personalizada para o aplicativo Web antes de expor para seus usuários. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir. 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

Na saída JSON, `defaultHostName` mostra o nome de domínio padrão de seu aplicativo Web. Em seu navegador, navegue até este endereço.

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>Etapa 2 - Configurar o mapeamento DNS

Nesta etapa, você adiciona um mapeamento de um domínio personalizado para o nome de domínio padrão de seu aplicativo Web, `<app_name>.azurewebsites.net`. Normalmente, você executa essa etapa no site do provedor de domínio. Cada site do registrador de domínio é um pouco diferente, portanto, você deve consultar a documentação do provedor. Estas são algumas diretrizes gerais. 

### <a name="navigate-to-to-dns-management-page"></a>Navegue até a página de gerenciamento do DNS

Primeiro, faça logon no site do registrador de domínio.  

Depois, localize a página para gerenciar registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. Normalmente, você pode encontrar o link exibindo as informações de conta e procurando um link como **Meus domínios**.

Quando encontrar a página, procure por um link que permita adicionar ou editar os registros DNS. Pode ser um **Arquivo de zona**, link de **Registros DNS** ou um link de configuração **Avançado**.

### <a name="create-a-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME que mapeie o nome de subdomínio desejado para o nome de domínio padrão de seu aplicativo Web (`<app_name>.azurewebsites.net`, em que `<app_name>` é o nome exclusivo do aplicativo).

Para o exemplo `www.contoso.com`, crie um CNAME que mapeie o nome de host `www` para `<app_name>.azurewebsites.net`.

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>Etapa 3 - Configurar o domínio personalizado em seu aplicativo Web

Quando você terminar de configurar o mapeamento do nome de host no site do provedor de domínio, você estará pronto para configurar o domínio personalizado em seu aplicativo Web. Use o comando [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) para adicionar essa configuração. 

No comando a seguir, substitua `<app_name>` pelo nome exclusivo de seu aplicativo, e <nome_do_seu_domínio> pelo nome de domínio personalizado totalmente qualificado (por exemplo, `www.contoso.com`). 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

O domínio personalizado agora está totalmente mapeado para seu aplicativo Web. Em seu navegador, navegue até o nome de domínio personalizado. Por exemplo:

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>Etapa 4 - Associar um certificado SSL personalizado ao seu aplicativo Web

Agora você tem um aplicativo Web, com o nome de domínio desejado na barra de endereços do navegador. No entanto, se você navegar até `https://<your_custom_domain>`, receberá um erro de certificado. 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

Esse erro ocorre porque seu aplicativo Web ainda não possui uma associação de certificado SSL que corresponda ao nome de domínio personalizado. No entanto, você não receberá um erro se navegar até `https://<app_name>.azurewebsites.net`. Isso porque seu aplicativo, bem como todos os aplicativos do Serviço de Aplicativo do Azure, são protegidos com o certificado SSL para o domínio curinga `*.azurewebsites.net` por padrão. 

Para acessar seu aplicativo Web pelo nome de seu domínio personalizado, associe o certificado SSL de seu domínio personalizado ao aplicativo Web. Você fará isso nesta etapa. 

### <a name="upload-the-ssl-certificate"></a>Carregar o certificado SSL

Carregue o certificado SSL de seu domínio personalizado em seu aplicativo Web usando o comando [az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload).

No comando a seguir, substitua `<app_name>` pelo nome de seu aplicativo exclusivo, `<path_to_ptx_file>` pelo caminho até o arquivo .PFX e `<password>` pela senha do certificado. 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

Após o carregamento do certificado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

Na saída JSON, `thumbprint` mostra a impressão digital do certificado carregado. Copie esse valor para a próxima etapa.

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>Associar o certificado SSL carregado ao aplicativo Web

Agora, seu aplicativo Web tem o nome de domínio personalizado que você deseja, e também tem um certificado SSL que protege esse domínio personalizado. Agora só resta associar o certificado carregado ao aplicativo Web. Faça isso usando o comando [az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind).

No comando a seguir, substitua `<app_name>` pelo nome exclusivo do aplicativo e `<thumbprint-from-previous-output>` pela impressão digital do certificado que você obteve com o comando anterior. 

az appservice web config ssl bind --name <nome_do_aplicativo> --resource-group myResourceGroup --certificate-thumbprint <miniatura-da-saída-anterior> --ssl-type SNI

Após a associação do certificado ao seu aplicativo Web, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<nome_do_aplicativo>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<nome_do_aplicativo>.azurewebsites.net", "<nome_do_aplicativo>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<nome_do_aplicativo>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<nome_do_aplicativo>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<nome_do_aplicativo>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<nome_do_aplicativo>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

Em seu navegador, navegue até o ponto de extremidade HTTPS de seu nome de domínio personalizado. Por exemplo:

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>Mais recursos

[Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure](custom-dns-web-site-buydomains-web-app.md)
[Comprar e configurar um certificado SSL para o Serviço de Aplicativo do Azure](web-sites-purchase-ssl-web-site.md)

