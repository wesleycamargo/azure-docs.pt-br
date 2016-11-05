---
title: Acesso condicional ao Azure Active Directory | Microsoft Docs
description: Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2016
ms.author: markvi

---
# Acesso condicional ao Azure Active Directory
A proteção do acesso aos recursos da empresa é importante para toda a organização. Com o advento dos serviços de nuvem e dos dispositivos móveis, a forma como os usuários acessam os recursos da empresa sofreu uma mudança significativa. A proliferação de dispositivos pessoais e corporativos exige uma nova abordagem ao acesso de recursos corporativos e à segurança.

## Para que serve o acesso condicional?
Os recursos de controle de acesso condicional do Azure Active Directory oferecem maneiras simples de as empresas protegerem recursos, tanto na nuvem quanto localmente. As políticas de acesso condicional podem proteger contra o risco de roubo e captura de credenciais com a autenticação multifator. Você também pode impor políticas de acesso condicional para proteger dados da empresa, de tal forma que apenas os dispositivos registrados em um sistema de gerenciamento de dispositivo móvel, como o Microsoft Intune, receba acesso a serviços confidenciais.

## Pré-requisitos
O acesso condicional do Azure Active Directory é um recurso do [Azure AD Premium](http://www.microsoft.com/identity). Todos os usuários que acessam um aplicativo com a política de acesso condicional aplicada deverão ter uma licença do Azure AD Premium. Você pode aprender mais sobre o uso com o [Relatório de usuário não licenciado](https://aka.ms/utc5ix).

## Como o controle de acesso condicional é imposto?
Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas que você escolhe para autenticar o usuário antes de permitir o acesso a um aplicativo. Quando esses requisitos de acesso forem atendidos, o usuário é autenticado e autorizado a acessar o aplicativo.

![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Condições
* **Associação a um grupo**: controle o nível de acesso de um usuário com base na associação dele a um ou mais grupos.
* **Local**: use o local do usuário para disparar a MFA (Autenticação multifator) e bloquear os controles quando um usuário não estiver em uma rede confiável.
* **Plataforma de dispositivo**: use o tipo de plataforma do dispositivo, como iOS, Android, Windows Mobile e Windows, como uma condição para a aplicação da política.
* **Dispositivo habilitado**: o estado habilitado/desabilitado do dispositivo é validado durante a avaliação da política do dispositivo. Ao desabilitar um dispositivo perdido ou roubado no diretório, ele não poderá ser usado para atender aos requisitos da política.
* **Risco do usuário e para entrar**: políticas de risco de Acesso Condicional estão disponíveis com o [Azure AD Identity Protection](active-directory-identityprotection.md) e fornecem proteção avançada com base em eventos de risco e atividades de entrada incomuns.

## Controles
* **MFA (Autenticação Multifator)**: você pode exigir autenticação forte com MFA. A MFA pode ser fornecida pelo Azure MFA ou por um provedor MFA local, usando o AD FS (Servidor de Federação do Active Directory). A MFA ajuda a proteger seus recursos de serem acessados por um usuário não autorizado que obteve acesso às credenciais de um usuário válido.
* **Bloco**: condições como o local do usuário podem ser aplicadas para bloquear o acesso de usuário. Por exemplo, o bloqueio do acesso quando um usuário não está em uma rede confiável.
* **Dispositivos compatíveis**: no nível do dispositivo, você pode definir políticas que impõem condições de modo que somente os computadores ingressados no domínio ou dispositivos móveis registrados em um aplicativo MDM (gerenciamento de dispositivo móvel) e que atendam aos requisitos de conformidade tenham permissão de acesso. Por exemplo, o Microsoft Intune pode ser usado para verificar a conformidade em dispositivos e reportá-la de volta ao Azure Active Directory para a imposição durante o acesso do aplicativo. Para obter orientações detalhadas sobre como usar o Microsoft Intune para proteger aplicativos e dados, consulte [Proteger os aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). Você também pode aplicar proteção de dados para dispositivos perdidos ou roubados através do Microsoft Intune. Para saber mais, confira [Ajudar a proteger seus dados com apagamento completo ou seletivo usando o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## Aplicativos
* O nível de acesso que você pode definir usando essas políticas pode ser aplicado a aplicativos e serviços na nuvem ou no local. A política é aplicada diretamente ao site ou serviço. A política é então imposta para acesso ao navegador, bem como a aplicativos que acessam o serviço. A lista de serviços aos quais a política pode ser aplicada pode ser encontrada aqui.

## Acesso condicional com base em dispositivo
Você também pode restringir o acesso a aplicativos de dispositivos que estejam registrados no Azure AD e que atendam a condições específicas. O acesso condicional baseado no dispositivo protege recursos organizacionais contra usuários que acessam esses recursos de:

* Dispositivos desconhecidos/não gerenciados
* Dispositivos que não atendam às políticas de segurança, conforme definido pela sua organização.

As políticas podem ser definidas com base nos seguintes requisitos:

* **Dispositivos ingressados no domínio** - você pode definir uma política para restringir o acesso a dispositivos que façam parte de um domínio do Active Directory local e também estejam registrados no Azure AD. Essa política aplica-se a desktops, laptops ou tablets da empresa executando Windows que pertençam a um domínio do Active Directory local que tenha sido registrado no Azure AD. Para saber mais sobre como configurar o registro automático de dispositivos Windows ingressados no domínio com o Azure AD, consulte [Como configurar o registro automático de dispositivos Windows ingressados no domínio com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
* **Dispositivos compatíveis** - você pode definir uma política para restringir o acesso a dispositivos marcados como **compatíveis** no diretório pelo sistema de gerenciamento. Essa política garante que apenas os dispositivos que atendam às políticas de segurança como imposição de criptografia de arquivos em um dispositivo tenham permissão para acesso. Essa política pode ser usada para restringir o acesso dos seguintes dispositivos:
  
  * **Dispositivos com Windows ingressados no domínio** gerenciados pelo System Center Configuration Manager 2016 (ramificação atual) implantados em uma configuração híbrida.
  * **Dispositivos móveis pessoais ou de trabalho do Windows 10** que são gerenciados pelo Microsoft Intune ou por um sistema de MDM (gerenciamento de dispositivo móvel) de terceiros com suporte.
  * **Dispositivos iOS e Android** gerenciados pelo Microsoft Intune.

Os usuários que acessam aplicativos protegidos pela política de AC baseada em dispositivo precisam fazer isso de dispositivos que atendam a essa política. O acesso será negado se o acesso for realizado de um dispositivo que não atenda à política.

Para obter informações sobre como configurar a política de AC baseada em dispositivo no Azure AD, consulte [Como configurar a política de acesso condicional com base no dispositivo para controle de acesso ao Azure Active Directory e aplicativos conectados](active-directory-conditional-access-policy-connected-applications.md).

## Índice do artigo para acesso condicional ao Azure Active Directory
O mapa de conteúdo a seguir lista os documentos que você precisa consultar para saber mais sobre como habilitar o acesso condicional em sua implantação atual

### Políticas de MFA e de localização
* [Introdução ao acesso condicional para aplicativos conectados ao Azure AD com base no grupo, no local e em políticas de MFA](active-directory-conditional-access-azuread-connected-apps.md)
* [Quais tipos de aplicativos têm suporte](active-directory-conditional-access-supported-apps.md)

### Acesso condicional com base em dispositivo
* [Como definir a política de acesso condicional com base no dispositivo para controle de acesso de aplicativos conectados no Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
* [Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Correção de usuário ao acessar aplicativos protegidos com acesso condicional baseado em dispositivos do Azure AD](active-directory-conditional-access-device-remediation.md)
* [Proteger dados em dispositivos perdidos ou roubados usando o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### Protegendo recursos com base em riscos de entrada
[Azure AD Identity Protection](active-directory-identityprotection.md)

### Informações adicionais
* [Perguntas frequentes sobre acesso condicional](active-directory-conditional-faqs.md)
* [Referência técnica](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0928_2016-->