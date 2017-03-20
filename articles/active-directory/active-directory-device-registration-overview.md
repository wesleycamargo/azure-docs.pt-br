---
title: "Visão geral do registro de dispositivos do Azure Active Directory | Microsoft Docs"
description: "O registro de dispositivo do Azure Active Directory é a base para cenários de acesso condicional com base no dispositivo. Quando um dispositivo é registrado, o registro de dispositivo do Azure Active Directory provisiona o dispositivo com uma identidade que é usada para autenticar o dispositivo quando o usuário faz logon."
services: active-directory
keywords: registro de dispositivos, habilitar registro de dispositivos, registro de dispositivos e MDM
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 1e92c1a2-01b8-4225-950b-373cd601b035
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: d19956e4964f57251f51eb8ffe5041c6a49da1a7
ms.lasthandoff: 03/10/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Introdução ao registro de dispositivos do Azure Active Directory
O registro de dispositivo do Azure Active Directory é a base para cenários de acesso condicional com base no dispositivo. Quando um dispositivo é registrado, o registro de dispositivo do Azure Active Directory fornece o dispositivo com uma identidade que é usada para autenticar o dispositivo quando o usuário faz logon. O dispositivo autenticado e os atributos desse dispositivo podem, em seguida, ser usados para impor políticas de acesso condicional para aplicativos locais e hospedados em nuvem.

Quando combinado com uma solução de MDM (gerenciamento de dispositivo móvel), como o Microsoft Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isso permite que você crie regras de acesso condicional que imponham que o acesso dos dispositivos atendam aos padrões de segurança e conformidade. Para saber mais sobre o registro de dispositivos no Microsoft Intune, veja [Registrar dispositivos para gerenciamento no Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Cenários habilitados por registro de dispositivos do Azure Active Directory
O registro de dispositivos do Azure Active Directory inclui suporte para dispositivos iOS, Android e Windows. Os cenários individuais que utilizam o registro de dispositivo do Azure AD podem ter requisitos e suporte de plataforma mais específicos. Esses cenários são os seguintes:

* **Acesso condicional a aplicativos que são hospedados localmente**: é possível usar dispositivos registrados com políticas de acesso para aplicativos configurados para usar o AD FS com o Windows Server 2012 R2. Para obter mais informações sobre como configurar o acesso condicional para local, consulte [Configurando o acesso condicional local usando o registro do dispositivo do Azure Active Directory](active-directory-device-registration-on-premises-setup.md).
* **Acesso condicional para aplicativos do Office 365 com o Microsoft Intune** : os administradores de TI podem provisionar políticas de dispositivo de acesso condicional para proteger recursos corporativos e, simultaneamente, permitir que os trabalhadores de informações em dispositivos compatíveis acessem os serviços. Para mais informações, consulte [Políticas de dispositivo de acesso condicional para serviços do Office 365](active-directory-conditional-access-device-policies.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Configuração do registro de dispositivos Azure Active Directory
Você precisa habilitar o registro de dispositivos do Azure AD no Portal do Azure para que os dispositivos móveis possam descobrir o serviço procurando registros DNS conhecidos. Você deve configurar o DNS da sua empresa para que os dispositivos Windows 10, Windows 8.1, Windows 7, Android e iOS possam descobrir e usar o serviço.
Você pode exibir e habilitar/desabilitar dispositivos registrados usando o Portal de administrador no Active Directory do Azure.

> [!NOTE]
> Para obter instruções mais recentes sobre como configurar o registro automático de dispositivos, veja [Como configurar o registro automático de domínio do Windows associado a dispositivos com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> 

### <a name="enable-azure-active-directory-device-registration-service"></a>Habilitar o serviço de registro de dispositivos do Azure Active Directory
1. Entre no portal do Microsoft Azure como um Administrador.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia **Diretório** , selecione o diretório.
4. Selecione a guia **Configurar** .
5. Role até a seção **Dispositivos**.
6. Selecione **TODOS** para **USUÁRIOS PODEM USAR DISPOSITIVOS COM INGRESSO NO LOCAL DE TRABALHO**.
7. Selecione o número máximo de dispositivos que deseja autorizar por usuário.

> [!NOTE]
> O registro com o Microsoft Intune ou o gerenciamento de dispositivos móveis para o Office 365 exige ingresso no local de trabalho. Se você tiver configurado qualquer um desses serviços, a opção TODOS está selecionada e o botão NONE está desabilitado.
> 
> 

Por padrão, a autenticação de dois fatores não está habilitada para o serviço. No entanto, a autenticação de dois fatores é recomendável ao registrar um dispositivo.

* Antes de solicitar a autenticação de dois fatores para esse serviço, você deve configurar um provedor de autenticação de dois fatores no Azure Active Directory e configurar suas contas de usuário para a Autenticação Multifator, veja [Adição da Autenticação Multifator ao Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Se estiver usando o AD FS com o Windows Server 2012 R2, configure um módulo de autenticação de dois fatores no AD FS. Veja [Usar a Autenticação Multifator com os Serviços de Federação do Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurar a descoberta de registro de dispositivos do Azure Active Directory
Os dispositivos com Windows 7 e Windows 8.1 descobrirão o serviço registro de dispositivos combinando o nome da conta de usuário com um nome do servidor de registro de dispositivos conhecido.

Você deve criar um registro DNS CNAME que aponte para o registro A associado ao serviço registro de dispositivos do Azure Active Directory. O registro CNAME deve usar o prefixo enterpriseregistration conhecido seguido pelo sufixo UPN usado pelas contas de usuário em sua organização. Se sua organização usa vários sufixos UPN, vários registros CNAME devem ser criados no DNS.

Por exemplo, se você usar dois sufixos UPN na sua organização chamados @contoso.com e @region.contoso.com, criará os registros DNS a seguir.

| Entrada | Tipo | Endereço |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Exibir e gerenciar objetos de dispositivo no Active Directory do Azure
1. No portal de administrador do Azure, você pode exibir, bloquear e desbloquear os dispositivos. Um dispositivo bloqueado não terá acesso aos aplicativos que estão configurados para permitir apenas os dispositivos registrados.
2. Faça logon no Portal do Microsoft Azure como um Administrador.
3. No painel esquerdo, selecione **Active Directory**.
4. Selecione seu diretório.
5. Clique a guia **Usuários** . Então, selecione um usuário para exibir seus dispositivos
6. Clique na guia **Dispositivos** .
7. Selecione **Dispositivos registrados** no menu suspenso.
8. Aqui você pode ver, bloquear ou desbloquear os dispositivos registrados dos usuários.

## <a name="additional-topics"></a>Tópicos adicionais
Você pode registrar seus dispositivos Ingressados no Domínio do Windows 7 e do Windows 8.1 com o registro de dispositivos do Azure AD. O tópico a seguir fornece mais informações sobre os pré-requisitos e as etapas necessárias para configurar o registro de dispositivos em dispositivos do Windows 7 e Windows 8.1.

* [Registro de dispositivo automático com o Active Directory do Azure para dispositivos de domínio associado do Windows](active-directory-conditional-access-automatic-device-registration.md)
* [Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows 10](active-directory-azureadjoin-devices-group-policy.md)


