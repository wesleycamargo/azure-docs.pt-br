---
title: Associar um certificado SSL personalizado existente a aplicativos Web do Azure | Microsoft Docs
description: Saiba como associar um certificado SSL personalizado a seu aplicativo Web, back-end do aplicativo móvel ou aplicativo de API no Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a543561658d593398ca74f8ae68dd6d0d27bcdaa
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636435"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Tutorial: vincular um certificado SSL personalizado existente aos Aplicativos Web do Azure

Os aplicativos Web do Azure fornecem um serviço de hospedagem na Web altamente escalonável, com aplicação automática de patches. Este tutorial mostra como associar um certificado SSL personalizado que você adquiriu de uma autoridade de certificação confiável para [aplicativos Web do Azure](app-service-web-overview.md). Quando você terminar, você poderá acessar seu aplicativo Web no ponto de extremidade HTTPS do seu domínio DNS personalizado.

![Aplicativo Web com certificado SSL personalizado](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar o tipo de preço do aplicativo
> * Associar seu certificado personalizado ao Serviço de Aplicativo
> * Renovar certificados
> * Impor HTTPS
> * Impor o TLS 1.1/1.2
> * Automatizar o gerenciamento de TLS com scripts

> [!NOTE]
> Se você precisar obter um certificado SSL personalizado, você poderá obtê-lo diretamente no Portal do Azure e associá-lo ao seu aplicativo Web. Siga o [tutorial de Certificados do Serviço de Aplicativo](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/)
- [Mapear um nome DNS personalizado para o aplicativo Web](app-service-web-tutorial-custom-domain.md)
- Adquirir um certificado SSL de uma autoridade de certificado confiável
- Esteja com a chave privada que foi usada para assinar a solicitação de certificado SSL

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Requisitos para o certificado SSL

Para usar o certificado no Serviço de Aplicativo, o certificado deve atender a todos os seguintes requisitos:

* Assinado por uma autoridade de certificado confiável
* Exportado como um arquivo PFX protegido por senha
* Conter chave privada com pelo menos 2.048 bits de extensão
* Conter todos os certificados intermediários na cadeia de certificados

> [!NOTE]
> Os **certificados ECC (Criptografia de Curva Elíptica)** podem funcionar com o Serviço de Aplicativo, mas não são abordados neste artigo. Trabalhe com sua autoridade de certificação seguindo exatamente estas etapas para criar certificados ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Associar o certificado SSL

Você está pronto para carregar seu certificado SSL para o aplicativo Web.

### <a name="merge-intermediate-certificates"></a>Mesclar certificados intermediários

Se a autoridade de certificação fornecer vários certificados na cadeia de certificados, você precisará mesclar os certificados na ordem. 

Para fazer isso, abra cada certificado recebido em um editor de texto. 

Crie um arquivo para o certificado mesclado, chamado _mergedcertificate.crt_. Em um editor de texto, copie o conteúdo de cada certificado para esse arquivo. A ordem de seus certificados deve seguir a ordem na cadeia de certificados, começando com o seu certificado e terminando com o certificado raiz. Ela se parece com o seguinte exemplo:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado SSL mesclado com a chave privada com a qual a solicitação de certificado foi gerada.

Se você gerou a solicitação de certificado usando o OpenSSL, isso significa que você criou um arquivo de chave privada. Para exportar o certificado para PFX, execute o comando a seguir. Substitua os espaços reservados _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a sua chave privada e o arquivo de certificado mesclado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando solicitado, defina uma senha de exportação. Você usará essa senha quando carregar o certificado SSL para o Serviço de Aplicativo posteriormente.

Se você usou o IIS ou o _Certreq.exe_ para gerar a solicitação de certificado, instale o certificado no computador local e, em seguida, [exporte o certificado para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Carregar o certificado SSL

Para carregar o certificado SSL, clique em **Configurações de SSL** no painel de navegação esquerdo de seu aplicativo Web.

Clique em **Carregar Certificado**. 

Em **Arquivo de Certificado PFX**, selecione o arquivo PFX. Em **Senha do certificado**, digite a senha que você criou ao exportar o arquivo PFX.

Clique em **Carregar**.

![Carregar um certificado](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Quando o Serviço de Aplicativo terminar de carregar o certificado, ele aparecerá na página **Configurações de SSL**.

![Certificado carregado](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Associar o certificado SSL

Na seção **Associações SSL**, clique em **Adicionar associação**.

Na página **Adicionar Associação SSL**, use os menus suspensos para selecionar o nome de domínio a ser protegido e o certificado a ser usado.

> [!NOTE]
> Se você carregou o certificado, mas não consegue ver os nomes de domínio no menu suspenso **Nome do host**, tente atualizar a página do navegador.
>
>

Em **Tipo SSL**, selecione se deseja usar **[SNI (Indicação de Nome de Servidor)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou SSL baseado em IP.

- **SSL baseado em SNI** – várias associações SSL baseadas em SNI podem ser adicionadas. Esta opção permite que vários certificados SSL protejam vários domínios no mesmo endereço IP. Navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) dão suporte ao SNI (encontre informações de suporte ao navegador mais abrangentes em [Indicação de Nome de Servidor](http://wikipedia.org/wiki/Server_Name_Indication)).
- **SSL baseado em IP** – apenas uma associação SSL baseada em IP pode ser adicionada. Esta opção permite apenas um certificado SSL para proteger um endereço IP público dedicado. Para proteger vários domínios, você deve protegê-los todos usando o mesmo certificado SSL. Essa é a opção tradicional para associação de SSL.

Clique em **Adicionar Associação**.

![Associar Certificado SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Quando o Serviço de Aplicativo terminar de carregar o certificado, ele aparecerá nas seções de **associações SSL**.

![Certificado associado ao aplicativo Web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registro para IP SSL

Se você não usar SSL com base em IP em seu aplicativo Web, pule para [Testar o HTTPS para seu domínio personalizado](#test).

Por padrão, o seu aplicativo Web usa um endereço IP público compartilhado. Quando você associa um certificado ao SSL baseado em IP, o Serviço de Aplicativo cria um novo endereço IP dedicado para o aplicativo Web.

Se você tiver mapeado um registro A para seu aplicativo Web, atualize o Registro do domínio com esse novo endereço IP dedicado.

A página **domínio personalizado** de seu aplicativo Web é atualizada com o novo endereço IP dedicado. [Copie esse endereço IP](app-service-web-tutorial-custom-domain.md#info) e, em seguida, [remapeie o registro](app-service-web-tutorial-custom-domain.md#map-an-a-record) para esse novo endereço IP.

<a name="test"></a>

## <a name="test-https"></a>Testar HTTPS

Agora, tudo o que resta fazer é certificar-se de que o HTTPS funcione com seu domínio personalizado. Em vários navegadores, navegue até `https://<your.custom.domain>` para ver se ele leva até seu aplicativo Web.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Se o seu aplicativo Web retorna erros de validação de certificado, você provavelmente está usando um certificado autoassinado.
>
> Se não for o caso, talvez você tenha deixado de fora certificados intermediários quando exportou o certificado para o arquivo PFX.

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>Renovar certificados

Seu endereço IP de entrada pode ser alterado ao excluir uma associação, mesmo se essa associação for baseada em IP. Isso é especialmente importante quando você renova um certificado que já está em uma associação com base em IP. Para evitar uma alteração no endereço IP do seu aplicativo, siga estas etapas pela ordem:

1. Carregar o novo certificado.
2. Associe o novo certificado para o domínio personalizado que você deseja sem excluir o antigo. Essa ação substitui a associação em vez de remover a antiga.
3. Exclua o certificado antigo. 

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, todos ainda podem acessar seu aplicativo Web usando HTTP. Você pode redirecionar todas as solicitações HTTP para a porta HTTPS.

Em sua página do aplicativo Web, na navegação esquerda, selecione **Configurações SSL**. Depois, em **HTTPS somente**, selecione **Ligado**.

![Impor HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Quando a operação estiver concluída, navegue até qualquer uma das URLs HTTP que aponte para seu aplicativo. Por exemplo: 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor as versões do TLS

Seu aplicativo permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por padrão, que é o nível TLS recomendado segundo os padrões do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões diferentes do TLS, execute estas etapas:

Em sua página do aplicativo Web, na navegação esquerda, selecione **Configurações SSL**. Depois, em **Versão do TLS**, selecione a versão mínima desejada do TLS. Essa configuração controla somente as chamadas de entrada. 

![Impor o TLS 1.1 ou 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Após a conclusão da operação, seu aplicativo rejeitará todas as conexões com versões inferiores do TLS.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar associações SSL para seu aplicativo Web com scripts, usando a [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando a seguir carrega um arquivo PFX exportado e obtém a impressão digital.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

O comando a seguir adiciona uma associação de SSL baseado em SNI usando a impressão digital do comando anterior.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

O comando a seguir impõe a versão mínima do TLS como 1.2.

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

O comando a seguir carrega um arquivo PFX exportado e adiciona uma associação de SSL baseado em SNI.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Certificados públicos (opcional)
Se seu aplicativo precisar acessar recursos remotos como um cliente e se o recurso remoto exigir autenticação de certificado, você poderá carregar [Certificados públicos](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) ao seu aplicativo Web. Os certificados públicos não são necessários para associações SSL de seu aplicativo.

Para obter mais detalhes sobre como carregar e usar um certificado público em seu aplicativo, confira [Usar um certificado SSL no seu código do aplicativo no Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-ssl-cert-load). Você também pode usar certificados públicos com aplicativos em Ambientes do Serviço de Aplicativo. Se você precisar armazenar o certificado no repositório de certificados LocalMachine, é preciso usar um aplicativo Web no Ambiente de Serviço de Aplicativo. Para obter mais informações, consulte [Como configurar certificados públicos para seu Aplicativo Web](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Carregar um certificado público](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Atualizar o tipo de preço do aplicativo
> * Associar seu certificado personalizado ao Serviço de Aplicativo
> * Renovar certificados
> * Impor HTTPS
> * Impor o TLS 1.1/1.2
> * Automatizar o gerenciamento de TLS com scripts

Vá para o próximo tutorial para saber como usar a Rede de Distribuição de Conteúdo do Azure.

> [!div class="nextstepaction"]
> [Adicionar uma CDN (Rede de Distribuição de Conteúdo) a um Serviço de Aplicativo do Azure](../cdn/cdn-add-to-web-app.md)

Para obter mais informações, consulte [Usar um certificado SSL no código do aplicativo no Serviço de Aplicativo do Azure](app-service-web-ssl-cert-load.md).
