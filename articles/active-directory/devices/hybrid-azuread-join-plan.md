---
title: Como planejar uma implementação de ingresso do Azure Active Directory híbrido no Azure Active Directory | Microsoft Docs
description: Saiba como configurar dispositivos adicionados ao Azure Active Directory híbrido.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2019
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b7f7fa4889742989a61f8cc076224d46f8eac2
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234095"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como: Planejar a sua implementação do ingresso do Azure Active Directory híbrido

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
|![Verificação][1]|Revise como controlar a junção híbrida do Microsoft Azure Active Directory de seus dispositivos|
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

Associação híbrida do Azure AD aos dispositivos de baixo nível do Windows:

- **É** suportado em ambientes não federados por meio do [Logon Único Contínuo do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **Não é** suportado ao usar a Autenticação de Passagem do Azure AD sem o Logon único sem emenda.

- **Não é** suportado ao usar roaming de credenciais ou roaming de perfil do usuário ou ao usar a VDI (Virtual Desktop Infrastructure).


O registro do Windows Server executando a função Controlador de Domínio (DC) não é suportado.

Se a sua organização exigir acesso à Internet por meio de um proxy de saída autenticado, você deverá garantir que os computadores com Windows 10 possam ser autenticados com êxito no proxy de saída. Como os computadores com Windows 10 executam o registro de dispositivos usando o contexto da máquina, é necessário configurar a autenticação de proxy de saída usando o contexto da máquina.


A associação híbrida do Azure AD é um processo para registrar automaticamente seus dispositivos associados ao domínio local com o Azure AD. Há casos em que você não quer que todos os seus dispositivos se registrem automaticamente. Se isso for verdade para você, consulte [Como controlar a associação híbrida do Azure AD aos seus dispositivos](hybrid-azuread-join-control.md).

Se os dispositivos incluídos no domínio do Windows 10 já estiverem [registrados pelo Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) em seu locatário, recomendamos remover esse estado antes de habilitar o ingresso no Azure AD Híbrido. No Windows 10 versão 1809, as seguintes alterações foram feitas para evitar esse estado duplo: 
 - Qualquer estado existente registrado pelo Azure Active Directory será automaticamente removido depois que os dispositivos forem incluídos no Azure Active Directory Híbrido. 
 - Você pode impedir que o seu dispositivo incluído no domínio seja registrado pelo Azure Active Directory adicionando esta chave do Registro: HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001


## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Revise como controlar a junção híbrida do Microsoft Azure Active Directory de seus dispositivos

O ingresso no Azure AD híbrido é um processo para registrar automaticamente os dispositivos ingressado no domínio local com Azure AD. Há casos em que não é necessário que todos os dispositivos sejam registrados automaticamente. Isso acontece, por exemplo, durante a distribuição inicial para verificar se tudo está funcionando conforme o esperado.

Para obter mais informações, consulte [Como controlar a associação híbrida do Microsoft Azure Active Directory aos seus dispositivos](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>Selecione o seu cenário

Você pode configurar o ingresso no Azure AD híbrido para os seguintes cenários:

- Domínios gerenciados
- Domínios federados  



Se seu ambiente tiver domínios gerenciados, ingresso no Azure AD híbrido oferece suporte a:

- PTA (Autenticação de Passagem)

- PHS (Sincronização de Hash de Senha)

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar o ingresso no Azure AD híbrido. O assistente permite simplificar significativamente o processo de configuração. Para obter mais informações, consulte:

- [Configurar o ingresso no Azure Active Directory híbrido para os domínios federados](hybrid-azuread-join-federated-domains.md)


- [Configurar o ingresso no Azure Active Directory híbrido para os domínios gerenciados](hybrid-azuread-join-managed-domains.md)


 Se a instalação da versão necessária do Azure AD Connect não for uma opção, consulte [como configurar manualmente o registro do dispositivo](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 


## <a name="on-premises-ad-upn-support-in-hybrid-azure-ad-join"></a>Suporte ao UPN do AD local no ingresso no Azure AD Híbrido

Às vezes, seus UPNs do AD local podem ser diferentes dos UPNs do Azure AD. Nesses casos, o ingresso no Azure AD Híbrido do Windows 10 dá suporte limitado aos UPNs do AD local com base no [método de autenticação](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), no tipo de domínio e na versão do Windows 10. Há dois tipos de UPNs do AD local que podem existir em seu ambiente:

 - UPN roteável: um nome UPN roteável tem um domínio verificado válido, que é registrado em um registrador de domínios. Por exemplo, se contoso.com é o domínio primário do Azure AD, contoso.org é o domínio primário no AD local pertencente à Contoso e [verificado no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
 
 - UPN não roteável: um UPN não roteável não tem um domínio verificado. É aplicável somente dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio primário no Azure AD, contoso.local é o domínio primário no AD local, mas não é um domínio verificável na Internet e é usado apenas na rede da Contoso.
 
A tabela a seguir fornece detalhes sobre o suporte a esses UPNs do AD local no ingresso no Azure AD Híbrido do Windows 10

|Tipo de UPN do AD local|Tipo de domínio|Versão do Windows 10|DESCRIÇÃO|
|-----|-----|-----|-----|
|Roteável|Federado |Da versão 1703|Disponível para o público geral|
|Roteável|Gerenciada|Da versão 1709|Atualmente em versão prévia privada. Não há suporte para SSPR do Azure AD |
|Não roteável|Federado|Da versão 1803|Disponível para o público geral|
|Não roteável|Gerenciada|Sem suporte||



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a junção híbrida do Active Directory do Azure para domínios federados](hybrid-azuread-join-federated-domains.md)
> [Configurar a junção híbrida do Active Directory do Azure para domínios gerenciados](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
