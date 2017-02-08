---
title: Instalar e gerenciar conectores - Proxy de aplicativo do Azure AD | Microsoft Docs
description: Aborda como criar e gerenciar grupos de conectores no Proxy de Aplicativo do Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 5404372d-3092-4054-aeee-26afb1399f33
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 7ed296a6773763f7aaae55e9a1667efca7d9e590
ms.openlocfilehash: c4fd1eb4637c310ff0c012a3aad5f4b8af47d50f


---
# <a name="publish-applications-on-separate-networks-and-locations-using-connectors---public-preview"></a>Publicar aplicativos em redes e locais separados usando conectores – Visualização Pública
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portal clássico do Azure](active-directory-application-proxy-connectors.md)
>
>

Os conectores e grupos de conectores são úteis para diversos cenários, incluindo:

* Sites com vários datacenters interconectados. Nesses casos, você deseja manter o tráfego dentro do datacenter o máximo possível, porque links entre datacenters são caros e lentos. Você pode implantar conectores em cada datacenter para servir apenas os aplicativos que residem dentro do datacenter. Essa abordagem minimiza os links entre datacenters e fornece uma experiência totalmente transparente para os usuários.
* Gerenciar aplicativos instalados em redes isoladas que não fazem parte da rede corporativa principal. Você pode usar grupos de conectores para instalar conectores dedicados em redes isoladas para isolar também os aplicativos para a rede.
* Para aplicativos instalados no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comum para proteger o acesso a todos os aplicativos. Os grupos de conectores não criam dependência adicional em sua rede corporativa nem fragmentam a experiência de aplicativo. Conectores podem ser instalados em cada datacenter na nuvem e servir apenas os aplicativos que residem nessa rede. Você pode instalar vários conectores para obter alta disponibilidade.
* Suporte para ambientes de várias florestas em que conectores específicos podem ser implantados por floresta e definidos para servir aplicativos específicos.
* Grupos de Conectores podem ser usados em sites de Recuperação de desastres para detectar failover ou como backup do site principal.
* Grupos de Conectores também podem ser usados para atender a várias empresas de um único locatário.

## <a name="prerequisite-create-your-connectors"></a>Pré-requisito: criar seus conectores
Para agrupar seus conectores, você precisa assegurar-se de ter [instalado vários conectores](active-directory-application-proxy-enable.md). Quando você instala um novo conector, ele automaticamente se junta ao grupo de conector **Padrão** .

## <a name="step-1-create-connector-groups"></a>Etapa 1: criar grupos de conectores
Você pode criar quantos grupos de conectores desejar. A criação de grupo de Conectores é feita no [portal do Azure](https://portal.azure.com).

1. Selecione **Azure Active Directory** para ir para o painel de gerenciamento para seu diretório. Aqui, selecione **Aplicativos empresariais** > **Proxy de aplicativo**.
2. Selecione o botão **Grupos de Conector** . A folha Novo Grupo de Conectores é exibida.
3. Nomeie o novo grupo de conectores e use o menu suspenso para selecionar quais conectores pertencem a esse grupo.
4. Selecione **Salvar** quando seu Grupo de conectores estiver concluído.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Etapa 2: atribuir aplicativos aos grupos de conectores
A última etapa é atribuir cada aplicativo ao grupo de conectores que vai servi-lo.

1. No painel de gerenciamento do seu diretório, selecione **Aplicativos empresariais** > **Todos os aplicativos** > o aplicativo que você deseja atribuir a um grupo de conectores > **Proxy de Aplicativo**.
2. Em **Grupo de conectores**, use o menu suspenso para selecionar o grupo que você deseja que o aplicativo use.
3. Clique em **Salvar** para aplicar a alteração.

## <a name="see-also"></a>Confira também
* [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)



<!--HONumber=Jan17_HO4-->


