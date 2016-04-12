<properties
	pageTitle="Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Active Directory do Azure | Microsoft Azure"  
    description="Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."  
    services="active-directory" 
	keywords="acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="03/23/2016"
	ms.author="femila"/>


# Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Active Directory do Azure  
  
A proteção do acesso aos recursos da empresa é importante para toda a organização. Com o advento dos serviços de nuvem e dos dispositivos móveis, a forma como os usuários acessam aos recursos das empresas sofreu uma mudança significativa. Isso exige mudanças na estratégia de proteção dos recursos corporativos.
  
## Para que serve o acesso condicional?  
 Os recursos de controle de acesso condicional no Active Directory do Azure oferecem maneiras simples de as empresas protegerem seus recursos, tanto na nuvem quanto localmente. Se você precisa de algo do tipo "impedir o acesso a meus recursos usando uma senha roubada" ou "exigir um dispositivo gerenciado íntegro para acessar meu conteúdo corporativo", o Active Directory do Azure atende às suas necessidades.

## Como o controle de acesso condicional é imposto?  
 Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas que você escolhe para autenticar o usuário antes de permitir o acesso a um aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Acesso a recursos baseado em usuário
  
- **Atributos de usuário**: no nível de atributos de usuário, você pode aplicar políticas para garantir que somente usuários autorizados possam acessar recursos da empresa.   
- **Associação de grupo de um usuário**: você também pode controlar o nível de acesso fornecido a um usuário baseado em sua participação em um ou mais grupos.   
- **MFA (autenticação multifator)**: além disso, você também pode impor políticas em que um usuário tem que autenticar sua identificação usando um sistema de autenticação multifator. Por exemplo, você pode forçar um usuário a confirmar um PIN em um celular pessoal para garantir uma camada extra de segurança. A autenticação MFA protege seus recursos de serem acessados por um usuário não autorizado que obteve acesso ao nome de usuário e à senha de um usuário válido. 

## Dispositivo baseado em acesso condicional 

- **Dispositivos registrados**: no nível do dispositivo, você pode definir políticas que impõem que somente dispositivos registrados ou conhecidos tenham permissão de acesso. Além disso, você pode usar uma solução MDM (Gerenciamento de Dispositivos Móveis) como o Microsoft Intune para garantir que somente dispositivos gerenciados acessem seus recursos. O acesso condicional no nível do dispositivo garante que apenas os dispositivos compatíveis com suas políticas, como impor o PIN em um dispositivo, tenham permissão de acesso. Além disso, usando as soluções MDM, você pode garantir que dados corporativos em um dispositivo perdido ou roubado sejam apagados remotamente.  
- **Políticas do dispositivo**: você também pode definir políticas para restringir o acesso com base somente em aplicativo. Além disso, você também pode definir o nível de acesso baseado na localização física do dispositivo, ou seja, se a solicitação está próxima de uma rede corporativa conhecida ou é externa.  

O nível de acesso que você pode definir usando essas políticas pode ser aplicado a recursos na nuvem ou no local. Os recursos na nuvem podem ser aplicativos como o Office 365 e aplicativos SaaS de terceiros. Além disso, eles também podem ser aplicativos de linha de negócios que você tem hospedado na nuvem.
  
## Acesso condicional - mapa de conteúdo  
O mapa de conteúdo a seguir lista os documentos que você precisa consultar para saber mais sobre como habilitar o acesso condicional em sua implantação atual


| Cenário | Artigos |
|------------------------------------------------------|----------|
| Proteger recursos corporativos contra ataques de phishing |[Introdução ao acesso condicional para aplicativos SaaS do AAD com MFA e extranet](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Acesso condicional para aplicativos do Azure AD](active-directory-conditional-access-technical-reference.md)<br><br>[Como configurar a MFA](multi-factor-authentication-get-started-cloud.md)<br><br>[Proteger recursos de nuvem com o Azure Multi-Factor Authentication e AD FS](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[Considerações de MFA por usuário](multi-factor-authentication-end-user-manage-settings.md)<br><br>[MFA de extranet](multi-factor-authentication-get-started-adfs-cloud.md)|
| Proteger dados corporativos em dispositivos perdidos ou roubados |[Serviço de registro do dispositivo](active-directory-conditional-access-device-registration-overview.md)<br><br> [Registrar dispositivos ingressados no domínio](active-directory-azureadjoin-setup.md)<br><br> [Usar dispositivos registrados no AD do Azure para acesso condicional local](active-directory-conditional-access-on-premises-setup.md) <br><br>[Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md) <br><br>[Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows8_1.md) <br><br>[Políticas do Dispositivo de Acesso Condicional para serviços do Office 365](active-directory-conditional-access-device-policies.md)|
| Informações Adicionais |[Perguntas frequentes sobre acesso condicional](active-directory-conditional-faqs.md)|

<!-----------HONumber=AcomDC_0330_2016-->