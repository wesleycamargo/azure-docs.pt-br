---
title: "Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure (GoDaddy)"
description: "Saiba como usar um nome de domínio do GoDaddy com Aplicativos Web do Azure"
services: app-service
documentationcenter: 
author: erikre
manager: erikre
editor: jimbe
ms.assetid: 33233e30-5846-488f-83f3-b32e5c114564
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 158c5dc06f83e16633d3c2fbb4eb27d3e8af030c
ms.lasthandoff: 04/21/2017


---
# <a name="configure-a-custom-domain-name-in-azure-app-service-purchased-directly-from-godaddy"></a>Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure (adquirido diretamente do GoDaddy)
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Se você tiver adquirido o domínio por meio de Aplicativos Web do Serviço de Aplicativo do Azure e consulte a etapa final de [Comprar domínio para aplicativos Web](custom-dns-web-site-buydomains-web-app.md).

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido diretamente de [GoDaddy](https://godaddy.com) com [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Compreendendo os registros DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Adicionar um registro DNS a seu domínio personalizado
Para associar seu domínio personalizado a um aplicativo Web no Serviço de Aplicativo, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo GoDaddy. Use as seguintes etapas para localizar as ferramentas DNS para o GoDaddy.com

1. Faça logon na sua conta do GoDaddy.com e selecione **Minha Conta** e, em seguida, **Gerenciar meus domínios**. Selecione o menu suspenso para o nome de domínio que você deseja usar com seu aplicativo Web do Azure e selecione **Gerenciar DNS**.
   
    ![página de domínio personalizado para GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)
2. Na página **Detalhes do domínio**, role até a guia **Arquivo de zona DNS**. Esta é a seção usada para adicionar e modificar registros DNS para o nome do domínio.
   
    ![Guia Arquivo de Zona DNS](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)
   
    Selecione **Adicionar registro** para adicionar um registro existente.
   
    Para **editar** um registro existente, selecione o ícone de lápis e papel ao lado do registro.
   
   > [!NOTE]
   > Antes de adicionar os novos registros, observe que o GoDaddy já criou os registros DNS para subdomínios populares (chamados **Host** no editor), como **email**, **arquivos**, **correio** e outros. Se o nome que você deseja usar já existir, modifique o registro existente em vez de criar um novo.
   > 
   > 
3. Ao adicionar um registro, você deve primeiro, selecionar o tipo de registro.
   
    ![selecione o tipo de registro](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)
   
    Em seguida, você deve fornecer o **Host** (o domínio ou subdomínio personalizado) e ao que ele **Aponta para**.
   
    ![adicionar um registro de zona](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)
   
   * Ao adicionar um **registro A (host)** – você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**), * (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **Aponta para** como o endereço IP do seu aplicativo Web do Azure.
   * Ao adicionar um **registro CNAME (alias)** - você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.azurewebsites.net** do aplicativo Web do Azure. Por exemplo, **contoso.azurewebsites.net**.
4. Clique em **Adicionar Outro**.
5. Selecione **TXT** como o tipo de registro e especifique um valor de **Host** de **@** e um valor de **Aponta para** de **&lt;yourwebappname&gt;.azurewebsites.net**.
   
   > [!NOTE]
   > Esse registro TXT é usado pelo Azure para validar que você possui o domínio descrito pelo registro A do primeiro registro TXT. Depois que o domínio tiver sido mapeado para o aplicativo Web no Portal do Azure, essa entrada do registro TXT poderá ser removida.
   > 
   > 
6. Ao concluir a adição ou a modificação dos registros, clique em **Concluir** para salvar as alterações.

<a name="enabledomain"></a>

## <a name="enable-the-domain-name-on-your-web-app"></a>Habilitar o nome do domínio no seu aplicativo Web
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


