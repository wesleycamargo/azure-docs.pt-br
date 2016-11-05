---
title: Trabalhando com Conectores de Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Aborda como criar e gerenciar grupos de conectores no Proxy de Aplicativo do Azure AD.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: kgremban

---
# Publicar aplicativos em redes e locais separados usando grupos de Conectores – Visualização Pública
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portal clássico do Azure](active-directory-application-proxy-connectors.md)
> 
> 

Grupos de conectores são úteis para diversos cenários, incluindo:

* Sites com vários datacenters interconectados. Nesses casos, você deseja manter o tráfego dentro do datacenter o máximo possível, porque links entre datacenters são caros e lentos. Você pode implantar conectores em cada datacenter para servir apenas os aplicativos que residem dentro do datacenter. Essa abordagem minimiza os links entre datacenters e fornece uma experiência totalmente transparente para os usuários.
* Gerenciar aplicativos instalados em redes isoladas que não fazem parte da rede corporativa principal. Você pode usar grupos de conectores para instalar conectores dedicados em redes isoladas para isolar também os aplicativos para a rede.
* Para aplicativos instalados no acesso à nuvem IaaS, os grupos de conector fornecem um serviço comum para proteger o acesso a todos os aplicativos. Grupos de conector não criam dependência adicional em sua rede corporativa nem fragmentam a experiência de aplicativo. Conectores podem ser instalados em cada datacenter na nuvem e servir apenas os aplicativos que residem nessa rede. Você pode instalar vários conectores para obter alta disponibilidade.
* Suporte para ambientes de várias florestas em que conectores específicos podem ser implantados por floresta e definidos para servir aplicativos específicos.
* Grupos de Conectores podem ser usados em sites de Recuperação de desastres para detectar failover ou como backup do site principal.
* Grupos de Conectores também podem ser usados para atender a várias empresas de um único locatário.

## Pré-requisito: criar seus conectores
Para agrupar seus conectores, você precisa assegurar-se de ter [instalado vários conectores](active-directory-application-proxy-enable.md). Quando você instala um novo conector, ele automaticamente se junta ao grupo de conector **Padrão**.

## Etapa 1: criar grupos de conectores
Você pode criar quantos grupos de conectores desejar. A criação de grupo de Conectores é feita no [portal do Azure](https://portal.azure.com).

1. Selecione **Azure Active Directory** para ir para o painel de gerenciamento para seu diretório. Nesse local, selecione **Aplicativos empresariais** > **Proxy de aplicativo**.
2. Selecione o botão **Grupos de Conector**. A folha Novo Grupo de Conectores é exibida.
3. Nomeie o novo grupo de conectores e use o menu suspenso para selecionar quais conectores pertencem a esse grupo.
4. Selecione **Salvar** quando seu Grupo de conectores estiver concluído.

## Etapa 2: atribuir aplicativos aos grupos de conectores
A última etapa é definir cada aplicativo ao grupo de conectores que ele vai atender.

1. No painel de gerenciamento para seu diretório, selecione **Aplicativos empresariais** > **Todos os aplicativos** > o aplicativo que você deseja atribuir a um grupo do conectores > **Proxy de Aplicativo**.
2. Em **Grupo de conectores**, use o menu suspenso para selecionar o grupo que você deseja que o aplicativo use.
3. Clique em **Salvar** para aplicar a alteração.

## Confira também
* [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0914_2016-->