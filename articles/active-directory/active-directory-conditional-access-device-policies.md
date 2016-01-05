<properties
	pageTitle="Políticas de dispositivo de acesso condicional para serviços do Office 365 | Microsoft Azure"
	description="Obter detalhes sobre o controle de condições baseado em dispositivo acessa os serviços do Office 365. Embora os IWs (operadores de informações) desejem acesso aos serviços do Office 365, como o Exchange e o SharePoint Online, no trabalho ou escola de seus dispositivos pessoais, seu administrador de TI deseja acesso seguro. Os administradores de TI podem provisionar políticas de dispositivo de acesso condicional para proteger recursos corporativos, ao mesmo tempo permitindo que IWs em dispositivos compatíveis acessem os serviços."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>
# Políticas de dispositivo de acesso condicional para serviços do Office 365

O termo "acesso condicional" tem muitas condições associadas a ele, como usuário autenticado multifator, dispositivo autenticado, dispositivo compatível, etc. Este tópico foca principalmente nas condições baseadas no dispositivo para controlar o acesso aos serviços do Office 365. Embora os IWs (operadores de informações) desejem acesso aos serviços do Office 365, como o Exchange e o SharePoint Online, no trabalho ou escola de seus dispositivos pessoais, seu administrador de TI deseja acesso seguro. Administradores de TI podem provisionar políticas de dispositivo de acesso condicional para proteger recursos corporativos, ao mesmo tempo permitindo que IWs em dispositivos compatíveis acessem os serviços. Políticas de acesso condicional para o Office 365 podem ser configuradas no portal de acesso condicional Microsoft Intune.

O Active Directory do Azure impõe políticas de acesso condicional para proteger o acesso aos serviços do Office 365. Um administrador de locatários pode criar uma política de acesso condicional que bloqueia um usuário em um dispositivo não compatível de acessar um serviço O365. O usuário deve obedecer às políticas de dispositivo da empresa antes do acesso poder ser concedido ao serviço. Como alternativa, o administrador também pode criar uma política que exige que os usuários simplesmente registrem seus dispositivos para acessar um serviço O365. As políticas podem ser aplicadas a todos os usuários de uma organização ou limitadas para alguns grupos de destino e aprimoradas para incluir grupos de destino adicionais.

É um pré-requisito para a imposição de políticas de dispositivo que os usuários registrem seus dispositivos com o serviço de registro de dispositivo do Active Directory do Azure. Você pode optar por habilitar a MFA (Multi-Factor Authentication) para o registro de dispositivos com o serviço de registro de dispositivo do Active Directory do Azure. A MFA é recomendada para o serviço de registro de dispositivo do Active Directory do Azure. Quando a MFA está habilitada, os usuários que registram seus dispositivos com o serviço de registro de dispositivo do Active Directory do Azure são desafiados para autenticação de segundo fator.

##Como funciona a política de acesso condicional?

Quando um usuário solicita acesso ao serviço O365 de uma plataforma de dispositivo com suporte, o Active Directory do Azure autentica o usuário e o dispositivo por meio do qual o usuário inicia a solicitação; e concede acesso ao serviço apenas quando o usuário está em conformidade com a política definida para o serviço. Os usuários que não têm seus dispositivos registrados recebem instruções de correção sobre como registrar e se tornarem compatíveis para acessar serviços corporativos do O365. Os usuários em dispositivos com Android e iOS precisarão registrar seus dispositivos usando o aplicativo de Portal da empresa. Quando um usuário registra seu dispositivo, o dispositivo está registrado no Active Directory do Azure e registrado para conformidade e gerenciamento de dispositivos. Os clientes devem usar o serviço de registro de dispositivo do Active Directory do Azure em conjunto com o Microsoft Intune para habilitar o gerenciamento de dispositivos móveis para o serviço do Office 365. Registro de dispositivo é um pré-requisito para os usuários acessarem os serviços do Office 365 quando as políticas do dispositivo são aplicadas.

Quando um usuário registra seu dispositivo com êxito, o dispositivo se tornará confiável. O Active Directory do Azure fornece logon único para acessar aplicativos da empresa e impõe política de acesso condicional para conceder acesso a um serviço não apenas na primeira vez que o usuário solicita acesso, mas sempre que o usuário solicitar renovar o acesso. O usuário terá o acesso negado a serviços quando as credenciais de logon forem alteradas, o dispositivo for perdido ou roubado ou a política não for atendida no momento da solicitação de renovação.

## Considerações de implantação:
Deve ser usado o serviço de registro de dispositivo do Active Directory do Azure para registro do dispositivo.

Quando os usuários são autenticados no local, os AD FS (Serviços de Federação do Active Directory) (1.0 e acima) são necessários. A Multi-Factor Authentication para ingresso no local de trabalho falhará quando o provedor de identidade não for capaz de fazer a autenticação multifator. Por exemplo, o AD FS 2.0 não é capaz de fazer a autenticação multi-fator. A administração de locatário deve garantir que o AD FS local seja capaz de MFA e que um método válido de MFA esteja habilitado, antes de habilitar a MFA no serviço de registro de dispositivo do Active Directory do Azure. Por exemplo, o AD FS no Windows Server 2012 R2 tem recursos MFA. Você também deve habilitar um método de autenticação (MFA) adicional válido no servidor do AD FS antes de habilitar a MFA no serviço de registro de dispositivo do Active Directory do Azure. Para obter mais informações sobre métodos MFA com suporte no AD FS, consulte Configurar métodos de autenticação adicionais do AD FS.

## Perguntas frequentes (FAQ)

P: Qual é a política de exclusão padrão para plataformas de dispositivos sem suporte?

R: No momento, as políticas de acesso condicional são impostas seletivamente aos usuários em dispositivos com Android e iOS. Aplicativos em outras plataformas de dispositivo não são, por padrão, afetados pela política de acesso condicional para dispositivos iOS e Android. A administração de locatário, no entanto, poderá substituir a política global para não permitir o acesso a usuários em plataformas sem suporte. Está prevista a extensão da política de acesso condicional a usuários em outras plataformas, incluindo o Windows.

P: Quando a política de acesso condicional para serviços do Office 365 será estendida para aplicativos baseados em navegador (por exemplo, OWA, SharePoint baseado em navegador).

R: No momento, o acesso condicional aos serviços do Office365 é limitado aos aplicativos sofisticados no dispositivo. Está prevista a extensão da política de acesso condicional a usuários que acessam serviços de navegadores.

<!---HONumber=AcomDC_1203_2015-->