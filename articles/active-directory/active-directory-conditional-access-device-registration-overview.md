<properties
	pageTitle="Visão geral do registro de dispositivos do Active Directory do Azure| Microsoft Azure"
	description="é a base para cenários de acesso condicional com base no dispositivo. Quando um dispositivo é registrado, o registro de dispositivo do Active Directory do Azure provisiona o dispositivo com uma identidade que é usada para autenticar o dispositivo quando o usuário faz logon."
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

# Visão geral do registro de dispositivos do Active Directory do Azure

O registro de dispositivo do Active Directory do Azure é a base para cenários de acesso condicional com base no dispositivo. Quando um dispositivo é registrado, o registro de dispositivo do Active Directory do Azure provisiona o dispositivo com uma identidade que é usada para autenticar o dispositivo quando o usuário faz logon. O dispositivo autenticado e os atributos desse dispositivo podem, em seguida, ser usados para impor políticas de acesso condicional para aplicativos locais e hospedados em nuvem. Quando combinado com uma solução de gerenciamento de dispositivos móveis, como Intune, os atributos do dispositivo no Active Directory do Azure serão atualizados com informações adicionais sobre o dispositivo. Isso permite que você crie regras de acesso condicional que impõe que o acesso dos dispositivos atendam aos padrões de segurança e conformidade. O registro de dispositivo do Active Directory do Azure está disponível no seu Active Directory do Azure. O serviço inclui suporte para dispositivos com iOS, Android e Windows. Os cenários individuais que utilizam o registro de dispositivo do Active Directory do Azure podem ter requisitos e suporte de plataforma mais específicos. Continue lendo para saber mais sobre os cenários específicos que estão atualmente disponíveis para você.

## Cenários habilitados por registro de dispositivos do Active Directory do Azure

O registro de dispositivos do AD do Azure pode ser pensado como a base para uma variedade de cenários. De modo geral, o serviço inclui suporte para dispositivos com iOS, Android e Windows. Os cenários individuais que utilizam o registro de dispositivo do AD do Azure podem ter requisitos e suporte de plataforma mais específicos. Esses cenários são os seguintes: acesso condicional para aplicativos que são hospedados localmente: você pode usar os dispositivos registrados com políticas de acesso para aplicativos que são configurados para usar o AD FS com o Windows Server 2012 R2. Para obter mais informações sobre como configurar o acesso condicional para local, consulte Configurando o acesso condicional local usando o registro do dispositivo do Active Directory do Azure.

Acesso condicional para aplicativos do Office 365 com Microsoft Intune: os administradores de TI podem provisionar políticas de dispositivo de acesso condicional para proteger recursos corporativos, ao mesmo tempo permitindo que trabalhos em dispositivos compatíveis acessem os serviços. Para mais informações, consulte Políticas de dispositivo de acesso condicional para serviços do Office 365

##Configuração do registro de dispositivos Active Directory do Azure

As seguintes configurações estão disponíveis para o serviço de registro de dispositivo do Active Directory do Azure: Habilitar registro de dispositivo do AD do Azure no Portal do Azure.

Dispositivos com Windows descobrem o serviço procurando registros DNS conhecidos. Você deve configurar o DNS da sua empresa para que os dispositivos com Windows 7 e Windows 8.1 possam descobrir e usar o serviço.

Você pode exibir e habilitar/desabilitar dispositivos registrados usando o Portal de administrador no Active Directory do Azure.

## Habilitar o registro de dispositivos do Active Directory do Azure
A seção a seguir descreve como habilitar o serviço de registro de dispositivo do Active Directory do Azure para seu diretório.
Habilitar serviço de registro de dispositivos do Active Directory do Azure
-------------------------------------------------------------
1. Faça logon no Portal do Azure como um Administrador.
1. No painel esquerdo, selecione **Active Directory**.
1. Na guia **Diretório**, selecione o diretório.
1. Selecione a guia **Configurar**.
1. Role até a seção **Dispositivos**.
1. Selecione **TODOS** para **USUÁRIOS PODEM USAR DISPOSITIVOS COM INGRESSO NO LOCAL DE TRABALHO**.
1. Selecione o número máximo de dispositivos que deseja autorizar por usuário.

>[AZURE.NOTE]O registro com o Microsoft Intune ou o gerenciamento de dispositivos móveis para o Office 365 exige ingresso no local de trabalho. Se você tiver configurado qualquer um desses serviços, a opção TODOS está selecionada e o botão NONE está desabilitado.


Por padrão, a autenticação de dois fatores não está habilitada para o serviço. No entanto, a autenticação de dois fatores é recomendável ao registrar um dispositivo.

* Antes de solicitar a autenticação de dois fatores para esse serviço, você deve configurar um provedor de autenticação de dois fatores no Active Directory do Azure e configurar suas contas de usuário para Multi-Factor Authentication. Consulte Adicionando Multi-Factor Authentication para o Active Directory do Azure

* Se estiver usando o AD FS com o Windows Server 2012 R2, você deve configurar um módulo de autenticação de dois fatores no AD FS. Consulte Usando Multi-Factor Authentication com Serviços de Federação do Active Directory

## Configurar a descoberta de registro de dispositivos do Active Directory do Azure
Os dispositivos com Windows 7 e Windows 8.1 vão descobrir o servidor de registro de dispositivo combinando o nome da conta de usuário com um nome do servidor de registro de dispositivos conhecido. Você deve criar um registro DNS CNAME que aponta para o registro A associado com o serviço de registro de dispositivo do Active Directory do Azure. O registro CNAME deve usar o prefixo enterpriseregistration conhecido seguido pelo sufixo UPN usado pelas contas de usuário em sua organização. Se sua organização usa vários sufixos UPN, vários registros CNAME devem ser criados no DNS.

Por exemplo, se você usar dois sufixos UPN na sua organização chamada @contoso.com e @region.contoso.com, você criará os seguintes registros DNS.
 
| Entrada | Tipo | Endereço |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Exibir e gerenciar objetos de dispositivo no Active Directory do Azure
1. No portal de administrador do Azure, você pode exibir, bloquear e desbloquear os dispositivos. Um dispositivo bloqueado não terá acesso aos aplicativos que estão configurados para permitir apenas os dispositivos registrados.
1. Faça logon no Portal do Microsoft Azure como um Administrador.
1. No painel esquerdo, selecione **Active Directory**.
1. Selecione seu diretório.
1. Clique a guia **Usuários**. Então, selecione um usuário para exibir seus dispositivos
1. Clique na guia **Dispositivos**.
1. Selecione **Dispositivos registrados** no menu suspenso.
1. Aqui você pode ver, bloquear ou desbloquear os dispositivos registrados dos usuários. 

## Tópicos adicionais

Você pode registrar seus dispositivos de domínio associado com Windows 7 e Windows 8.1 com o registro de dispositivos do AD do Azure. O tópico a seguir fornece mais informações sobre os pré-requisitos e as etapas necessárias para configurar o registro de dispositivos em dispositivos do Windows 7 e Windows 8.1.

- [Registro de dispositivo automático com o Active Directory do Azure para dispositivos de domínio associado do Windows](active-directory-conditional-access-automatic-device-registration.md) 
- [Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

<!---HONumber=AcomDC_1203_2015-->