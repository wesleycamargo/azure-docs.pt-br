<properties
	pageTitle="Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Active Directory do Azure | Microsoft Azure"  
    description="Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."  
    services="active-directory" 
	keywords="acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional" 
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/23/2016"
	ms.author="femila"/>


# Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Active Directory do Azure  
  
A proteção do acesso aos recursos da empresa é importante para toda a organização. Com o advento dos serviços de nuvem e dos dispositivos móveis, a forma como os usuários acessam aos recursos das empresas sofreu uma mudança significativa. Isso exige mudanças na estratégia de proteção dos recursos corporativos.
  
## Para que serve o acesso condicional?  
 Os recursos de controle de acesso condicional no Active Directory do Azure oferecem maneiras simples de as empresas protegerem seus recursos, tanto na nuvem quanto localmente. Se você precisa de algo do tipo "impedir o acesso a meus recursos usando uma senha roubada" ou "exigir um dispositivo gerenciado íntegro para acessar meu conteúdo corporativo", o Active Directory do Azure atende às suas necessidades.

## Como o controle de acesso condicional é imposto?  
 Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas que você escolhe para autenticar o usuário antes de permitir o acesso a um aplicativo. Quando esses requisitos de acesso forem atendidos, o usuário é autenticado e autorizado a acessar o aplicativo.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Acesso a recursos baseado em usuário
  
- **Atributos de usuário**: no nível de atributos de usuário, você pode aplicar políticas para garantir que somente usuários autorizados possam acessar recursos da empresa.
- **Associação de grupo de um usuário**: você também pode controlar o nível de acesso fornecido a um usuário baseado em sua participação em um ou mais grupos.
- **MFA (autenticação multifator)**: você também pode impor políticas em que um usuário tem que autenticar sua identificação usando um sistema de autenticação multifator. Por exemplo, você pode forçar um usuário a confirmar um PIN em um celular pessoal para garantir uma camada extra de segurança. A autenticação MFA protege seus recursos de serem acessados por um usuário não autorizado que obteve acesso ao nome de usuário e à senha de um usuário válido.
- **Risco do usuário e para entrar**: políticas de risco de Acesso Condicional estão disponíveis com o Azure AD Identity Protection e fornecem proteção avançada com base em eventos de risco e atividades de entrada incomuns.
 

## Acesso condicional com base em dispositivo 

**Dispositivos registrados**: no nível do dispositivo, você pode definir políticas que impõem que somente dispositivos registrados de MDM (Gerenciamento de Dispositivo Móvel) permissão de acesso. O Microsoft Intune é usado para validar se o dispositivo é compatível e registrado. O acesso condicional no nível do dispositivo garante que apenas os dispositivos compatíveis com suas políticas, como impor a criptografia de arquivo em um dispositivo, tenham permissão de acesso. Além disso, usando as soluções MDM, você pode garantir que dados corporativos em um dispositivo perdido ou roubado sejam apagados remotamente.
  

O nível de acesso que você pode definir usando essas políticas pode ser aplicado a recursos na nuvem ou no local. Os recursos na nuvem podem ser aplicativos como o Office 365 e aplicativos SaaS de terceiros. Além disso, eles também podem ser aplicativos de linha de negócios que você tem hospedado na nuvem.
  
## Acesso condicional - mapa de conteúdo  
O mapa de conteúdo a seguir lista os documentos que você precisa consultar para saber mais sobre como habilitar o acesso condicional em sua implantação atual


| Cenário | Artigos |
|------------------------------------------------------|----------|
| Proteger os recursos com base no nível de autenticação ou usuário |[Introdução ao acesso condicional aos aplicativos conectados do Azure AD com base no grupo, no local e na confidencialidade do aplicativo](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Que tipos de aplicativos têm suporte](active-directory-conditional-access-supported-apps.md)|
| Proteger dados corporativos em dispositivos perdidos ou roubados |[Ajudar a proteger seus dados com apagamento completo ou seletivo usando o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)|
|Protegendo recursos com base em riscos de entrada |[Azure AD Identity Protection](active-directory-identityprotection.md) |
| Informações Adicionais |[Perguntas frequentes sobre o Acesso Condicional](active-directory-conditional-faqs.md)<br><br>[Referência técnica](active-directory-conditional-access-technical-reference.md) |

<!---HONumber=AcomDC_0629_2016-->