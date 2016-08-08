<properties
	pageTitle="Acesso condicional ao Azure Active Directory | Microsoft Azure"  
    description="Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."  
    services="active-directory" 
	keywords="acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="07/26/2016"
	ms.author="markvi"/>


# Acesso condicional ao Azure Active Directory   
  
A proteção do acesso aos recursos da empresa é importante para toda a organização. Com o advento dos serviços de nuvem e dos dispositivos móveis, a forma como os usuários acessam aos recursos das empresas sofreu uma mudança significativa. Isso requer uma nova abordagem de segurança.
  
## Para que serve o acesso condicional?  

Os recursos de controle de acesso condicional no Azure Active Directory oferecem maneiras simples de as empresas protegerem recursos, tanto na nuvem quanto localmente. As políticas de acesso condicional podem ser usadas para ajudar a proteger contra o risco de roubo e de captura de credenciais ao exigirem autenticação multifator e ao ajudarem a proteger dados da empresa, exigindo que um dispositivo gerenciado do Intune conceda acesso aos serviços confidenciais.



## Requisitos de licenciamento

O acesso condicional é um recurso do [Azure AD Premium](http://www.microsoft.com/identity). Todos os usuários que acessam um aplicativo com a política de acesso condicional aplicada deverão ter uma licença do Azure AD Premium. Você pode aprender mais sobre o uso com o [Relatório de usuário não licenciado](https://aka.ms/utc5ix).





## Como o controle de acesso condicional é imposto?  

Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas que você escolhe para autenticar o usuário antes de permitir o acesso a um aplicativo. Quando esses requisitos de acesso forem atendidos, o usuário é autenticado e autorizado a acessar o aplicativo.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Condições
  
- **Associação a um grupo**: você pode controlar o nível de acesso de um usuário com base na associação dele a um ou mais grupos.

- **Local**: você pode usar o local do usuário para disparar o MFA e bloquear controles quando um usuário não estiver em uma rede confiável.

- **Plataforma de dispositivo**: você pode usar o tipo de plataforma do dispositivo, como iOS, Android, Windows Mobile e Windows, como uma condição para a aplicação da política.

- **Dispositivo habilitado**: dispositivo habilitado/desabilitado é validado durante a avaliação da política de dispositivo. Ao desabilitar um dispositivo perdido ou roubado no diretório, ele não poderá ser usado para atender aos requisitos da política.

- **Risco do usuário e para entrar**: políticas de risco de Acesso Condicional estão disponíveis com o Azure AD Identity Protection e fornecem proteção avançada com base em eventos de risco e atividades de entrada incomuns.


## Controles
   
- **Autenticação multifator (MFA)**: você pode exigir autenticação forte com MFA. A MFA pode ser fornecida pelo Azure MFA ou por um provedor MFA local, usando o ADFS. A autenticação MFA ajuda a proteger seus recursos de serem acessados por um usuário não autorizado que obteve acesso ao nome de usuário e à senha de um usuário válido.

- **Bloqueio**: o acesso pode ser aplicado sob condições como o local do usuário. Por exemplo, o bloqueio do acesso quando um usuário não está em uma rede confiável.

- **Dispositivos registrados/compatíveis**: no nível do dispositivo, você pode definir políticas que imponham que somente dispositivos compatíveis e registrados no MDM (Gerenciamento de Dispositivo Móvel) tenham permissão de acesso. O Microsoft Intune é usado para validar se o dispositivo é compatível e registrado. O acesso condicional no nível do dispositivo então garante que apenas os dispositivos compatíveis com a política do MDM terão permissão de acesso.
 

## Aplicativos

- O nível de acesso que você pode definir usando essas políticas pode ser aplicado a aplicativos e serviços na nuvem ou no local. A política é aplicada diretamente ao site ou serviço. A política é então imposta para acesso ao navegador, bem como a aplicativos que acessam o serviço. A lista de serviços aos quais a política pode ser aplicada pode ser encontrada aqui.


  
## Acesso condicional - mapa de conteúdo  
O mapa de conteúdo a seguir lista os documentos que você precisa consultar para saber mais sobre como habilitar o acesso condicional em sua implantação atual


### Políticas de MFA e de local

- [Introdução ao acesso condicional para aplicativos conectados ao Azure AD com base no grupo, no local e em políticas de MFA](active-directory-conditional-access-azuread-connected-apps.md)
- [Quais tipos de aplicativos têm suporte](active-directory-conditional-access-supported-apps.md)


### Políticas de dispositivo

[Ajudar a proteger os dados que exigem o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### Protegendo recursos com base em riscos de entrada

[Azure AD Identity Protection](active-directory-identityprotection.md)

### Informações Adicionais

- [Perguntas frequentes sobre acesso condicional](active-directory-conditional-faqs.md)
- [Referência técnica](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0727_2016-->