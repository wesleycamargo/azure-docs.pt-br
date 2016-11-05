---
title: Configurar um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure que usa o Gerenciador de Tráfego para balanceamento de carga.
description: Use um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure que inclua o Gerenciador de Tráfego para balanceamento de carga.
services: app-service\web
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: robmcm

---
# Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [introdução](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas sobre como usar um nome de domínio personalizado com o Serviço de Aplicativo do Azure que usa o Gerenciador de Tráfego para balanceamento de carga.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## Compreendendo os registros DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## Configurar seus aplicativos Web para o modo Padrão
[!INCLUDE [modos](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Adicionar um registro DNS a seu domínio personalizado
> [!NOTE]
> Se você tiver adquirido o domínio por meio de Aplicativos Web do Serviço de Aplicativo do Azure, ignore etapas a seguir e consulte a etapa final do artigo [Comprar domínio para aplicativos Web](custom-dns-web-site-buydomains-web-app.md).
> 
> 

Para associar seu domínio personalizado a um aplicativo Web no Serviço de Aplicativo do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo registrador de domínio do qual comprou seu nome de domínio. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1. Entre em sua conta em seu registrador de domínios e procure uma página de gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nomes**. Normalmente, um link para essa página pode ser encontrado exibindo-se as informações de conta e procurando-se um link como **Meus domínios**.
2. Depois de localizar a página de gerenciamento para seu nome de domínio, procure um link que permita editar os registros DNS. Ele pode estar listado como um **Arquivo de zona**, **Registros DNS** ou como um link de configuração **Avançado**.
   
   * A página deverá ter alguns poucos registros já criados, como uma associação de entrada '**@**' ou '*' com uma página de 'domain parking'. Ela também pode conter registros para subdomínios comuns como **www**.
   * A página mencionará **registros CNAME** ou fornecerá uma lista suspensa para selecionar um tipo de registro. Ela também pode mencionar outros registros, como **registros A** e **registros MX**. Em alguns casos, os registros CNAME serão chamados por outros nomes como um **Registro de Alias**.
   * A página também terá campos que permitem **mapear** de um **Nome do host** ou **Nome de domínio** para outro nome de domínio.
3. Embora as especificidades de cada registrador variem, em geral, você mapeia *de* seu nome de domínio personalizado (como **contoso.com**,) *para* o nome de domínio do Gerenciador de Tráfego (**contoso.trafficmanager.net**) que é usado para seu aplicativo web.
   
   > [!NOTE]
   > Como alternativa, se um registro já está em uso e você precisa associar preventivamente seus aplicativos a ele, pode criar um registro CNAME adicional. Por exemplo, para associar preventivamente **www.contoso.com** a seu aplicativo Web, crie um registro CNAME de **awverify.www** para **contoso.trafficmanager.net**. Você pode adicionar "www.contoso.com" ao seu aplicativo Web sem alterar o registro CNAME "www". Para obter mais informações, confira [Criar registros DNS para um aplicativo Web em um domínio personalizado][CREATEDNS].
   > 
   > 
4. Depois de terminar a adição ou a modificação de registros DNS no registrador, salve as alterações.

<a name="enabledomain"></a>

## Habilitar o Gerenciador de tráfego
[!INCLUDE [modos](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## Próximas etapas
Para saber mais, confira o [Centro de desenvolvedores do Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md

<!---HONumber=AcomDC_0921_2016-->