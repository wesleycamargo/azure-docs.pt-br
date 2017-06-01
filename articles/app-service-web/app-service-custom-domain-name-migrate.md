---
title: "Migrar um domínio personalizado ativo para o Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como migrar um domínio personalizado que já está atribuído a um site ativo para seu aplicativo no Serviço de Aplicativo do Azure sem qualquer tempo de inatividade."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 6adc34f208c6c4363b73f6e42b4a4d6ae2e4483f
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>Migrar um domínio personalizado ativo para o Serviço de Aplicativo do Azure

Este artigo mostra como migrar um domínio personalizado ativo para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) sem qualquer tempo de inatividade.

Ao migrar um site ativo e seu nome de domínio para o Serviço de Aplicativo, esse nome de domínio já estará fornecendo tráfego ativo, por isso você tentará evitar qualquer tempo de inatividade na resolução DNS durante o processo de migração. Nesse caso, você precisa associar preventivamente o nome de domínio ao seu aplicativo do Azure para verificação do domínio. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já saiba [mapear manualmente um domínio personalizado para o Serviço de Aplicativo](app-service-web-tutorial-custom-domain.md).

## <a name="bind-the-domain-name-preemptively"></a>Vincular o nome de domínio preventivamente

Ao vincular um domínio personalizado preventivamente, você obtém o seguinte antes mesmo de fazer quaisquer alterações nos registros DNS:

- Verificar a propriedade de domínio
- Habilitar o nome de domínio para seu aplicativo

Quando você finalmente altera o registro DNS a fim de apontar para seu aplicativo do Serviço de Aplicativo, os clientes são redirecionados do site antigo para o seu aplicativo do Serviço de Aplicativo sem qualquer tempo de inatividade na resolução DNS.

Siga as etapas abaixo:

1. Primeiro, crie um registro TXT de verificação com seu Registro DNS, seguindo as etapas em [Criar o(s) registros DNS](app-service-web-tutorial-custom-domain.md#create-a).
O registro TXT adicional assume a convenção que mapeia de &lt;*subdomain*>.&lt;*rootdomain*> para &lt;*appname*>.azurewebsites.net.
Consulte a tabela a seguir para ver exemplos:  
 
    <table cellspacing="0" border="1">
    <tr>
    <th>Exemplo de FQDN</th>
    <th>Host TXT</th>
    <th>Valor TXT</th>
    </tr>
    <tr>
    <td>contoso.com (raiz)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com (sub)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com (curinga)</td>
    <td>awverify\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. Em seguida, adicione seu nome de domínio personalizado ao aplicativo do Azure seguindo as etapas em [Habilitar o nome de domínio personalizado para seu aplicativo](app-service-web-tutorial-custom-domain.md#enable-a).

    Agora, seu domínio personalizado está habilitado no aplicativo do Azure. A única coisa que resta a fazer é atualizar o registro DNS com seu registrador de domínios.

3. Por fim, atualize o registro DNS de seu domínio para apontar para o aplicativo do Azure, conforme mostrado em [Criar o(s) registro(s) DNS](app-service-web-tutorial-custom-domain.md#create-a). 

    O tráfego de usuários deverá ser redirecionado para o aplicativo do Azure imediatamente após a ocorrência da propagação de DNS.

## <a name="next-steps"></a>Próximas etapas
Saiba como proteger seu nome de domínio personalizado com HTTPS [comprando um certificado SSL no Azure](web-sites-purchase-ssl-web-site.md) ou [usando um certificado SSL de outro lugar](app-service-web-tutorial-custom-ssl.md).

> [!NOTE]
> Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

[Introdução ao DNS do Azure](../dns/dns-getstarted-create-dnszone.md)  
[Criar registros DNS para um aplicativo Web em um domínio personalizado](../dns/dns-web-sites-custom-domain.md)  
[Delegar domínio para o DNS do Azure](../dns/dns-domain-delegation.md)


