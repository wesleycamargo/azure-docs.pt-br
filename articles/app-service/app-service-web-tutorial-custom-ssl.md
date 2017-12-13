---
title: Associar um certificado SSL personalizado existente a aplicativos Web do Azure | Microsoft Docs
description: "Saiba como associar um certificado SSL personalizado a seu aplicativo Web, back-end do aplicativo móvel ou aplicativo de API no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f69bc731b2858c338d7f7b4d347e7107a0f4eeed
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Associar um certificado SSL personalizado existente a aplicativos Web do Azure

Os Aplicativos Web do Azure fornecem um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patch. Este tutorial mostra como associar um certificado SSL personalizado que você adquiriu de uma autoridade de certificação confiável para [aplicativos Web do Azure](app-service-web-overview.md). Quando você terminar, você poderá acessar seu aplicativo Web no ponto de extremidade HTTPS do seu domínio DNS personalizado.

![Aplicativo Web com certificado SSL personalizado](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar o tipo de preço do aplicativo
> * Associar o certificado SSL personalizado ao Serviço de Aplicativo
> * Impor HTTPS para seu aplicativo
> * Automatizar a associação de certificado SSL com scripts

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

## <a name="prepare-your-web-app"></a>Preparar o aplicativo Web

Para associar um certificado SSL personalizado ao aplicativo Web, o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) deve estar na camada **Básica**, **Standard** ou **Premium**. Nesta etapa, você precisa ter certeza de que seu aplicativo Web está no tipo de preço com suporte.

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Abra o [Portal do Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navegue até seu aplicativo Web

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo Web.

![Selecionar aplicativo Web](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Você aterrissou na página de gerenciamento do aplicativo Web.  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da página do aplicativo Web, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Certifique-se de que o aplicativo Web não esteja na camada **Gratuita** ou **Compartilhada**. A camada atual do seu aplicativo Web é realçada por uma caixa azul escuro.

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Não há suporte para SSL personalizado nas camadas **Gratuito** ou **Compartilhado**. Se precisar escalar verticalmente, siga as etapas da próxima seção. Caso contrário, feche a página **Escolher o tipo de preço** e vá para [Carregar e associar o certificado SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente seu Plano do Serviço de Aplicativo

Selecione uma das camadas **Básico**, **Standard** ou **Premium**.

Clique em **Selecionar**.

![Escolha um tipo de preço](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

![Escalar verticalmente a notificação](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

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

Para carregar o certificado SSL, clique em **Certificados SSL** no painel de navegação esquerdo do aplicativo Web.

Clique em **Carregar Certificado**. 

Em **Arquivo de Certificado PFX**, selecione o arquivo PFX. Em **Senha do certificado**, digite a senha que você criou ao exportar o arquivo PFX.

Clique em **Carregar**.

![Carregar um certificado](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Quando o Serviço de Aplicativo terminar de carregar o certificado, ele aparecerá na página **Certificados SSL**.

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

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, todos ainda podem acessar seu aplicativo Web usando HTTP. Você pode redirecionar todas as solicitações HTTP para a porta HTTPS.

Na sua página do aplicativo Web, na navegação esquerda, selecione **Domínios personalizados**. Depois, em **HTTPS somente**, selecione **Ligado**.

![Impor HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Quando a operação estiver concluída, navegue até qualquer uma das URLs HTTP que aponte para seu aplicativo. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

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

### <a name="azure-powershell"></a>PowerShell do Azure

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
Você pode carregar [certificados públicos](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) para seu aplicativo Web. Você também pode usar certificados públicos para aplicativos em Ambientes de Serviço de Aplicativo. Se você precisar armazenar o certificado no repositório de certificados LocalMachine, é preciso usar um aplicativo Web no Ambiente de Serviço de Aplicativo. Para obter mais informações, consulte [Como configurar certificados públicos para seu Aplicativo Web](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Carregar um certificado público](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Atualizar o tipo de preço do aplicativo
> * Associar o certificado SSL personalizado ao Serviço de Aplicativo
> * Impor HTTPS para seu aplicativo
> * Automatizar a associação de certificado SSL com scripts

Vá para o próximo tutorial para saber como usar a Rede de Distribuição de Conteúdo do Azure.

> [!div class="nextstepaction"]
> [Adicionar uma CDN (Rede de Distribuição de Conteúdo) a um Serviço de Aplicativo do Azure](app-service-web-tutorial-content-delivery-network.md)

Para obter mais informações, consulte [Usar um certificado SSL no código do aplicativo no Serviço de Aplicativo do Azure](app-service-web-ssl-cert-load.md).