---
title: "Adicionar um domínio personalizado ao seu ponto de extremidade CDN | Microsoft Docs"
description: "Saiba como mapear o conteúdo da CDN do Azure para um domínio personalizado."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: 8c5dd3ddd03b3531e4ffb7b622110a2ea997f9ae
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>Adicionar um domínio personalizado ao seu ponto de extremidade CDN
Depois de criar um perfil, você normalmente também cria um ou mais pontos de extremidade CDN (um subdomínio de azureedge.net) para distribuir seu conteúdo usando HTTP e HTTPS. Por padrão, esse ponto de extremidade está incluído em todas as suas URLs, por exemplo, `http(s)://contoso.azureedge.net/photo.png`). Para sua conveniência, a CDN do Azure fornece a opção de associar um domínio personalizado (por exemplo, `www.contoso.com`) ao seu ponto de extremidade. Com essa opção, você pode usar um domínio personalizado para entregar seu conteúdo, em vez do seu ponto de extremidade. Essa opção será útil se, por exemplo, você quiser que seu próprio nome de domínio fique visível aos seus clientes para fins de identificação de marca.

Se você ainda não tiver um domínio personalizado, deverá primeiro adquirir um com um provedor de domínio. Depois de obter um domínio personalizado, siga estas etapas:
1. [Acessar os registros DNS do seu provedor de domínio](#step-1-access-dns-records-by-using-your-domain-provider)
2. [Criar os registros de DNS CNAME](#step-2-create-the-cname-dns-records)
    - Opção 1: mapeamento direto do seu domínio personalizado para o ponto de extremidade CDN
    - Opção 2: mapeamento de seu domínio personalizado para o ponto de extremidade CDN usando cdnverify 
3. [Habilitar o mapeamento de registro CNAME no Azure](#step-3-enable-the-cname-record-mapping-in-azure)
4. [Verifique se o subdomínio personalizado referencia seu ponto de extremidade da CDN](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(Etapa dependente) Mapear o domínio personalizado permanente para o ponto de extremidade da CDN](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>Etapa 1: acessar registros de DNS por meio de seu provedor de domínio

Se você estiver usando o Azure para hospedar seus [domínios DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview), delegue o DNS do provedor de domínio a um DNS do Azure. Para obter mais informações, confira [Delegar um domínio ao DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

Caso contrário, se você estiver usando o provedor de domínio para lidar com seu domínio do DNS, entre no site do seu provedor de domínio. Localize a página de gerenciamento de registros DNS consultando a documentação do provedor ou procurando áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nome**. Muitas vezes, você pode encontrar a página de registros DNS exibindo as informações da conta e procurando um link como **Meus domínios**. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes.

> [!NOTE]
> Para alguns provedores, como GoDaddy, as alterações nos registros DNS só entram em vigor quando você seleciona um link separado **Salvar Alterações**. 


## <a name="step-2-create-the-cname-dns-records"></a>Etapa 2: criar os registros de DNS CNAME

Antes de usar um domínio personalizado com um ponto de extremidade da CDN do Azure, primeiro crie um registro de CNAME (Nome Canônico) com seu provedor de domínio. Um registro CNAME é um tipo de registro no sistema DNS (Sistema de Nomes de Domínio) que mapeia um domínio de origem para um domínio de destino especificando um nome de domínio de alias para o nome de domínio "canônico" ou verdadeiro. Para a CDN do Azure, o domínio de origem é o seu domínio personalizado (e o subdomínio) e o domínio de destino é o ponto de extremidade da CDN. A CDN do Azure verifica o registro de DNS CNAME quando você adiciona o domínio personalizado ao ponto de extremidade do portal ou API. 

Um registro CNAME mapeia um domínio específico e o subdomínio, como `www.contoso.com` ou `cdn.contoso.com`; não é possível mapear um registro CNAME para um domínio raiz, como `contoso.com`. Um subdomínio pode ser associado a apenas um ponto de extremidade CDN e o registro CNAME que você cria roteará todo o tráfego endereçado ao subdomínio para o ponto de extremidade especificado. Por exemplo, se você associar `www.contoso.com` ao ponto de extremidade da sua CDN, não poderá associá-lo a outro ponto de extremidade do Azure, como um ponto de extremidade de conta de armazenamento ou um ponto de extremidade de serviço de nuvem. No entanto, você pode usar outros subdomínios do mesmo domínio para pontos de extremidade de serviço diferentes. Você também pode mapear outros subdomínios para o mesmo ponto de extremidade da CDN.

Use uma das opções a seguir para mapear seu domínio personalizado para um ponto de extremidade CDN:

- Opção 1: mapeamento direto. Se nenhum tráfego de produção estiver em execução no domínio personalizado, você poderá mapear um domínio personalizado para um ponto de extremidade CDN diretamente. O processo de mapeamento de seu domínio personalizado para o ponto de extremidade da CDN pode resultar em um breve período de inatividade para o domínio, enquanto você está registrando o domínio no Portal do Azure. Sua entrada de mapeamento de CNAME deve estar neste formato: 
 
  | NOME             | TYPE  | VALUE                  |
  |------------------|-------|------------------------|
  | www\.consoto.com | CNAME | consoto\.azureedge.net |


- Opção 2: mapeamento com o subdomínio **cdnverify**. Se o tráfego de produção que não puder ser interrompido estiver em execução no domínio personalizado, você poderá criar um mapeamento CNAME temporário para o ponto de extremidade CDN. Com essa opção, use o subdomínio **cdnverify** do Azure para fornecer uma etapa de registro intermediária para que os usuários possam acessar seu domínio sem interrupção durante o mapeamento de DNS.

   1. Crie um novo registro CNAME e forneça um alias de subdomínio que inclua o subdomínio **cdnverify**. Por exemplo, **cdnverify.www** ou **cdnverify.cdn**. 
   2. Forneça o nome de host, que é o ponto de extremidade CDN, no seguinte formato: `cdnverify.<EndpointName>.azureedge.net`. Sua entrada de mapeamento de CNAME deve estar neste formato: 

   | NOME                       | TYPE  | VALUE                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.consoto.com | CNAME | cdnverify.consoto\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>Etapa 3: habilitar o mapeamento de registro CNAME no Azure

Depois de registrar seu domínio personalizado usando um dos procedimentos anteriores, você pode habilitar o recurso de domínio personalizado na CDN do Azure. 

1. Faça logon no [portal do Azure](https://portal.azure.com/) e navegue até o perfil CDN com o ponto de extremidade que você deseja mapear para um domínio personalizado.  
2. Na folha **Perfil da CDN**, clique no ponto de extremidade da CDN ao qual você deseja associar o subdomínio.
3. No canto superior esquerdo da folha de ponto de extremidade, clique em **Domínio personalizado**. 

   ![Botão de domínio personalizado](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. Na caixa de texto **Nome de host personalizado**, digite seu domínio personalizado, incluindo o subdomínio. Por exemplo, `www.contoso.com` ou `cdn.contoso.com`.

   ![Caixa de diálogo Adicionar um domínio personalizado](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. Clique em **Adicionar**.

   O Azure verificará se o registro do CNAME existe para o nome de domínio que você digitou. Se o CNAME estiver correto, seu domínio personalizado será validado. Pode levar algum tempo para o registro CNAME se propagar para os servidores de nome. Se o seu domínio não for validado imediatamente, verifique se o registro CNAME está correto, aguarde alguns minutos e tente novamente. Para pontos de extremidade da **CDN do Azure da Verizon** (Standard e Premium), pode levar até 90 minutos para as configurações de domínio personalizado serem propagadas para todos os nós de borda da CDN.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Etapa 4: verifique se o subdomínio personalizado referencia seu ponto de extremidade da CDN

Depois de concluir o registro do seu domínio personalizado, verifique se o subdomínio personalizado referencia seu ponto de extremidade da CDN.
 
1. Verifique se você tem conteúdo público armazenado em cache no ponto de extremidade. Por exemplo, se o ponto de extremidade da CDN estiver associado a uma conta de armazenamento, a CDN armazenará conteúdo em cache em contêineres de blobs públicos. Para testar o domínio personalizado, verifique se o contêiner está definido para permitir acesso público e contém pelo menos um blob.

2. Em seu navegador, navegue até o endereço do blob usando o domínio personalizado. Por exemplo, se o seu domínio personalizado for `cdn.contoso.com`, a URL para o blob armazenado em cache deverá ser semelhante à seguinte URL: `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>Etapa 5 (etapa dependente): mapear o domínio personalizado permanente para o ponto de extremidade da CDN

Esta etapa depende da etapa 2, opção 2 (mapeamento com o subdomínio **cdnverify**). Se você estiver usando o subdomínio **cdnverify** temporário e tiver verificado que ele funciona, poderá mapear seu domínio personalizado permanente para o ponto de extremidade da CDN.

1. No site do provedor de domínio, crie um registro de DNS CNAME para mapear seu domínio personalizado permanente para o ponto de extremidade da CDN. Sua entrada de mapeamento de CNAME deve estar neste formato: 
 
   | NOME             | TYPE  | VALUE                  |
   |------------------|-------|------------------------|
   | www\.consoto.com | CNAME | consoto\.azureedge.net |
2. Exclua o registro CNAME com o subdomínio **cdnverify** que você criou anteriormente.

## <a name="see-also"></a>Consulte também
[Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure](cdn-create-new-endpoint.md)  
[Delegar seu domínio ao DNS do Azure](../dns/dns-domain-delegation.md)