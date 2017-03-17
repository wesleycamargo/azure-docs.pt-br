---
title: "Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory| Microsoft Docs"
description: "Configure seus dispositivos de domínio do Windows para serem registrados de forma automática e silenciosa com o Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9934902811354ffa4047d70d995a6dd44be0229b
ms.lasthandoff: 03/10/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Introdução ao registro de dispositivos do Azure Active Directory

O registro de dispositivo do Azure Active Directory é a base para cenários de acesso condicional com base no dispositivo. Quando um dispositivo é registrado, o registro de dispositivo do Azure Active Directory fornece o dispositivo com uma identidade que é usada para autenticar o dispositivo quando o usuário faz logon. O dispositivo autenticado e os atributos desse dispositivo podem, em seguida, ser usados para impor políticas de acesso condicional para aplicativos locais e hospedados em nuvem.

Quando combinado com uma solução de MDM (gerenciamento de dispositivo móvel), como o Microsoft Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isso permite que você crie regras de acesso condicional que imponham que o acesso dos dispositivos atendam aos padrões de segurança e conformidade. Para saber mais sobre o registro de dispositivos no Microsoft Intune, veja Registrar dispositivos para gerenciamento no Intune.
Os cenários habilitados pelo Registro de Dispositivos do Azure Active Directory inclui suporte para dispositivos iOS, Android e Windows. Os cenários individuais que utilizam o registro de dispositivo do AD do Azure podem ter requisitos e suporte de plataforma mais específicos. 

Esses cenários são os seguintes:

- **Acesso condicional para aplicativos do Office 365 com o Microsoft Intune:** os administradores de TI podem provisionar políticas de dispositivo de acesso condicional para proteger recursos corporativos e, simultaneamente, permitir que os trabalhadores de informações em dispositivos compatíveis acessem os serviços. Para saber mais, veja Políticas de dispositivo de acesso condicional para serviços do Office 365.

- ** Acesso Condicional a aplicativos que são hospedados localmente:** é possível usar dispositivos registrados com políticas de acesso para aplicativos configurados para usar o AD FS com o Windows Server 2012 R2. Para obter mais informações sobre como configurar o acesso condicional para local, consulte [Configurando o acesso condicional local usando o registro do dispositivo do Azure Active Directory](active-directory-device-registration-on-premises-setup.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Configuração do registro de dispositivos Active Directory do Azure

Para configurar o registro de dispositivo, há várias opções:

- Os dispositivos podem ser registrados ao ingressar no domínio do Azure AD. Para saber mais sobre esse tópico, aprenda mais sobre o ingresso do Azure AD e as configurações necessárias para os usuários ingressarem no domínio do Azure AD.

- Os dispositivos podem ser registrados quando os usuários adicionarem contas corporativas ou de estudante ao Windows em um dispositivo pessoal, ou quando os dispositivos móveis se conectarem a um recurso de trabalho que exige registro. Para garantir isso, habilite o Registro de Dispositivos do Azure AD no Portal do Azure. 

- Os dispositivos podem ser registrados usando o registro automático de dispositivo para computadores tradicionais ingressados em domínio. Para garantir isso, você deve primeiro configurar Azure AD Connect antes de continuar com o registro automático.

Para obter as instruções mais recentes, confira [Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).  
Você também pode exibir e habilitar ou desabilitar dispositivos registrados usando o Portal de administrador no Azure Active Directory.

## <a name="enable-the-azure-active-directory-device-registration-service"></a>Habilitar o serviço de registro de dispositivos do Azure Active Directory

**Para habilitar o serviço de registro de dispositivos do Azure Active Directory**

1.    Entre no Portal do Microsoft Azure como administrador.

2.    No painel esquerdo, selecione **Active Directory**.

3.    Na guia Diretório, selecione o diretório.

4.    Clique em **Configurar**.

5.    Role até **Dispositivos**.

6.    Selecione TODOS para OS USUÁRIOS PODEM REGISTRAR SEUS DISPOSITIVOS COM O AZURE AD.

7.    Selecione o número máximo de dispositivos que deseja autorizar por usuário.

O registro com o Microsoft Intune ou o Gerenciamento de Dispositivos Móveis para o Office 365 exige registro do dispositivo. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NONE** estará desabilitado. Verifique se eles estão configurados corretamente e possuem o licenciamento apropriado.

Por padrão, a autenticação de dois fatores não está habilitada para o serviço. No entanto, a autenticação de dois fatores é recomendável ao registrar um dispositivo.

- Antes de solicitar a autenticação de dois fatores para esse serviço, você deve configurar um provedor de autenticação de dois fatores no Active Directory do Azure e configurar suas contas de usuário para Multi-Factor Authentication. Consulte Adicionando Multi-Factor Authentication para o Active Directory do Azure

- Se estiver usando o AD FS com o Windows Server 2012 R2, configure um módulo de autenticação de dois fatores no AD FS. Confira Usar a Autenticação Multifator com os Serviços de Federação do Active Directory.

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Exibir e gerenciar objetos de dispositivo no Active Directory do Azure

No portal de administrador do Azure, você pode exibir, bloquear e desbloquear os dispositivos. Um dispositivo bloqueado não terá acesso aos aplicativos que estão configurados para permitir apenas os dispositivos registrados.

**Para exibir e gerenciar objetos de dispositivo no Azure Active Directory**
 
1.    Faça logon no Portal do Microsoft Azure como administrador.

2.    No painel esquerdo, selecione **Active Directory**.

3.    Selecione seu diretório.

4.    Selecione **Usuários**. 

5.  Clique no usuário para o qual você deseja ver os dispositivos.

6.    Selecione **Dispositivos**.

7.    Selecione **Dispositivos Registrados**.

Agora você pode ver, bloquear ou desbloquear os dispositivos registrados dos usuários.
Dispositivos com Windows 10 ingressados em um domínio local e automaticamente registrados não aparecem na guia Usuários. Use o comando do PowerShell Get-MsolDevice para localizar todos os dispositivos da empresa. 


## <a name="next-steps"></a>Próximas etapas

Para configurar o registro automatizado do dispositivo, confira [Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



