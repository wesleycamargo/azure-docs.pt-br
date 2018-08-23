---
title: Como configurar dispositivos adicionados ao Azure Active Directory híbrido | Microsoft Docs
description: Saiba como configurar dispositivos adicionados ao Azure Active Directory híbrido.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: fbc40d0768a7cf1cb83a3e78dd524fac26254bea
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145058"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como planejar sua implementação de junção híbrida do Active Directory do Azure

De maneira semelhante a um usuário, um dispositivo está se tornando outra identidade que você deseja proteger e também usa para proteger seus recursos a qualquer hora e local. É possível atingir essa meta, colocando as identidades dos dispositivos no Azure AD usando um dos métodos a seguir:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Ao colocar os dispositivos no Azure AD, você maximiza a produtividade dos usuários por meio de SSO (logon único) em toda a nuvem e recursos locais. Ao mesmo tempo, é possível proteger o acesso à sua nuvem e aos recursos locais com [acesso condicional](../active-directory-conditional-access-azure-portal.md).

Caso tenha um ambiente local do Active Directory e queira ingressar dispositivos adicionados ao domínio no Azure AD, você poderá fazer isso configurando dispositivos adicionados ao Azure AD híbrido. Este artigo fornece as etapas relacionadas para implementar uma associação híbrida do Azure AD em seu ambiente. 


## <a name="prerequisites"></a>Pré-requisitos

Este artigo presume que você esteja familiarizado com o [Introdução ao gerenciamento de dispositivos no Active Directory do Azure](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação híbrida do AD do Azure, você deve se familiarizar com:

|   |   |
|---|---|
|![Verificação][1]|Dispositivos com suporte de revisão|
|![Verificação][1]|Você deve saber de coisas de revisão|
|![Verificação][1]|Selecione o seu cenário|


 


## <a name="review-supported-devices"></a>Dispositivos com suporte de revisão 

Ingresso no Azure AD híbrido oferece suporte a dispositivos de uma ampla variedade de Windows. Como a configuração para dispositivos que executam versões mais antigas do Windows requer etapas adicionais ou diferentes, os dispositivos com suporte são agrupados em duas categorias:

**Dispositivos atuais do Windows**

- Windows 10
    
- Windows Server 2016


Para os dispositivos que executam o sistema operacional da área de trabalho do Windows, a versão com suporte é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a última versão do Windows 10.



 **Dispositivos de nível inferior do Windows**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


Como primeira etapa do planejamento, você deve revisar seu ambiente e determinar se precisa dar suporte a dispositivos de baixo nível do Windows.



## <a name="review-things-you-should-know"></a>Você deve saber de coisas de revisão

Você não pode usar uma associação híbrida do Azure AD se o seu ambiente consistir em uma única floresta que sincronize dados de identidade com mais de um locatário do Azure AD.

Se você estiver contando com a Ferramenta de Preparação do Sistema (Sysprep), crie imagens a partir de uma instalação do Windows que não tenha sido configurada para associação híbrida do Azure AD.

Se você estiver contando com um instantâneo de Máquina Virtual (VM) para criar VMs adicionais, use um instantâneo de VM que não tenha sido configurado para associação híbrida do Azure AD.

O registro de dispositivos de baixo nível do Windows não é suportado para dispositivos configurados para roaming de perfil de usuário ou credenciais roaming. Se você depender da mobilidade de perfis ou configurações, use o Windows 10.

- O registro de dispositivos de nível inferior do Windows **tem** suporte em ambientes não federados por meio do Logon Único Contínuo [Logon Único Contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
 
- **Não há** suporte para o registro de dispositivos de nível inferior do Windows ao usar a autenticação de passagem do Azure AD sem logon único contínuo.

- O registro de dispositivos de nível inferior do Windows **não tem** suporte para dispositivos que utilizam perfis móveis. Se você depender da mobilidade de perfis ou configurações, use o Windows 10.


O registro do Windows Server executando a função Controlador de Domínio (DC) não é suportado.

Se a sua organização exigir acesso à Internet por meio de um proxy de saída autenticado, você deverá garantir que os computadores com Windows 10 possam ser autenticados com êxito no proxy de saída. Como os computadores com Windows 10 executam o registro de dispositivos usando o contexto da máquina, é necessário configurar a autenticação de proxy de saída usando o contexto da máquina.


A associação híbrida do Azure AD é um processo para registrar automaticamente seus dispositivos associados ao domínio local com o Azure AD. Há casos em que você não quer que todos os seus dispositivos se registrem automaticamente. Se isso for verdade para você, consulte [Como controlar a associação híbrida do Azure AD aos seus dispositivos](hybrid-azuread-join-control.md).



## <a name="select-your-scenario"></a>Selecione o seu cenário

Você pode configurar o ingresso no Azure AD híbrido para os seguintes cenários:

- Domínios gerenciados
- Domínios federados  



Se seu ambiente tiver domínios gerenciados, ingresso no Azure AD híbrido oferece suporte a:

- Autenticação de passagem (PTA) com o logon único contínuo (SSO) 

- Senha tem PHS (sincronização) com o logon único contínuo (SSO) 

Começando com a versão 1.1.819.0, Azure AD Connect fornece um Assistente para configurar o ingresso no Azure AD híbrido. O assistente permite simplificar significativamente o processo de configuração. Para obter mais informações, consulte:

- [Configurar o ingresso no Azure Active Directory híbrido para os domínios federados](hybrid-azuread-join-federated-domains.md)

- [Configurar o ingresso no Azure Active Directory híbrido para os domínios gerenciados](hybrid-azuread-join-managed-domains.md)


 Se a instalação da versão necessária do Azure AD Connect não for uma opção, consulte [como configurar manualmente o registro do dispositivo](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a junção híbrida do Active Directory do Azure para domínios federados](hybrid-azuread-join-federated-domains.md)
> [Configurar a junção híbrida do Active Directory do Azure para domínios gerenciados](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
