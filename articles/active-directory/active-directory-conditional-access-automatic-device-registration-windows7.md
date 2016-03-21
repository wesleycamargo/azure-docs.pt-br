<properties
	pageTitle="#Configurar o Registro Automático de Dispositivo para dispositivos ingressados no domínio do Windows 7| Microsoft Azure"
	description="As etapas para configurar seus dispositivos ingressados no domínio do Windows 7 para registro automático com o AD do Azure e as etapas para implantar o pacote de software de registro de dispositivo nos seus dispositivos ingressados no domínio do Windows 7 usando um sistema de distribuição de software, como o System Center Configuration Manager."
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
	ms.date="03/07/2016"
	ms.author="femila"/>

# Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 7

Como um administrador de TI, você pode configurar seus dispositivos ingressados no domínio do Windows 7 para registrarem-se automaticamente com o AD do Azure. Para fazer isso, você deve implantar o pacote de software de registro de dispositivo ao seus dispositivos ingressados no domínio do Windows 7 usando um sistema de distribuição de software, como o System Center Configuration Manager. Certifique-se de ler e concluir os pré-requisitos listados no Registro Automático de Dispositivo com dispositivos ingressados no domínio do Windows do Active Directory do Azure.

##Instalar o pacote de software de registro de dispositivo em dispositivos ingressados no Windows 7

O registro de dispositivo para o Windows 7 está disponível como um [pacote MSI que pode ser baixado](https://connect.microsoft.com/site1164). O pacote deve ser instalado em computadores Windows 7 que fazem parte de um domínio do Active Directory. Você deve implantar o pacote usando um sistema de distribuição de software como o System Center Configuration Manager. O pacote MSI dá suporte às opções de instalação silenciosa padrão com o parâmetro /quiet. O pacote de software está disponível para download no [site do Microsoft Connect](https://connect.microsoft.com/site1164). Aqui você pode selecionar e baixar o Ingresso no Local de Trabalho para o Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## Ingresso no Local de Trabalho com o Active Directory do Azure
O registro de dispositivo para dispositivos ingressados no domínio do Windows 7 não exige ou inclui uma interface do usuário. Depois de instalado no computador, qualquer usuário que fizer logon no computador do domínio será automaticamente e silenciosamente registrado com um objeto de dispositivo no AD do Azure. Haverá um objeto de dispositivo no AD do Azure para todos os usuários registrados do dispositivo físico.

O instalador cria uma Tarefa Agendada no sistema que é executada no contexto do usuário e é disparada na entrada do usuário. A tarefa registra silenciosamente o usuário e o dispositivo com o AD do Azure depois que a entrada usuário for concluída. A Tarefa Agendada pode ser encontrada na Biblioteca do Agendador de Tarefas em **Microsoft** > **Ingresso no Local de Trabalho**. A tarefa será executada e registrada em todo e qualquer usuário do Active Directory que entrar no computador. A ilustração a seguir lista o processo passo a passo para registro automático.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Um usuário (trabalhador de informações) faz logon em um computador cliente do Windows 7 usando credenciais de domínio do Active Directory.
1. A tarefa agendada Ingresso no Local de Trabalho é executada.
1. O usuário é autenticado silenciosamente no AD FS usando a Autenticação Integrada do Windows.
1. O computador do Windows 7 é registrado para o usuário no AD do Azure.
1. Um objeto de dispositivo e um certificado é criado no AD do Azure. O objeto representa o user@device.
1. O certificado do Ingresso no Local de Trabalho é armazenado no computador.

## Cancelando o registro dos dispositivos ingressados no domínio do Windows 7

Você pode optar por cancelar o registro dos seus dispositivos ingressados no domínio do Windows 7 fazendo o seguinte: desinstale o pacote de software do Ingresso no Local de Trabalho dos dispositivos ingressados no domínio do Windows 7 usando um sistema de distribuição de software como o System Center Configuration Manager.

Em seguida, abra um prompt de comando no computador com o Windows 7 e execute o seguinte comando para cancelar o registro do dispositivo:
    
    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
Este comando deve ser executado no contexto de cada usuário do domínio que tenha entrado no computador. Visualizador de Eventos e Erros de dispositivos ingressados no domínio do Windows 7.

O Log de Eventos do Windows no computador Windows 7 exibirá as mensagens relacionadas ao Ingresso no Local de Trabalho. Você pode encontrar mensagens de eventos de Ingresso no Local de Trabalho bem-sucedidas e malsucedidas. O Log de Eventos pode ser encontrado no Visualizador de Eventos em Logs de Aplicativos e Serviços > Ingresso no Local de Trabalho da Microsoft.

## Tópicos adicionais

- [Visão geral do registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md)
- [Registro de dispositivo automático com o Active Directory do Azure para dispositivos de domínio associado do Windows](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows8_1.md)
- [Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows 10](active-directory-azureadjoin-devices-group-policy.md)

 

<!---HONumber=AcomDC_0309_2016-->