<properties 
	pageTitle="Instalação do Agente do Azure AD Connect Health para AD FS | Microsoft Azure"
	description="Esta é a página do Azure AD Connect Health que descreve a instalação do agente dos Serviços de Federação do Active Directory (AD FS)."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="billmath"/>






# Instalação do Agente do Azure AD Connect Health para AD FS

Este documento vai orientá-lo a instalar e configurar o Agente do Azure AD Connect Health para AD FS em seus servidores.

>[AZURE.NOTE]Lembre-se de que, antes de você ver todos os dados em sua instância do Azure AD Connect Health, é necessário instalar o agente do Azure AD Connect Health nos servidores de destino. Não se esqueça de concluir os requisitos [aqui](active-directory-aadconnect-health.md#requirements) antes de instalar o agente. Você pode baixar o agente [aqui](http://go.microsoft.com/fwlink/?LinkID=518973).


## Instalação do agente
Para iniciar a instalação do agente, clique duas vezes no arquivo .exe que você baixou. Na primeira tela, clique em instalar.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Depois que a instalação for concluída, clique em Configurar agora.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Isso iniciará um prompt de comando, seguido pelo PowerShell que executará Register-AzureADConnectHealthADFSAgent. Você será solicitado a entrar no Azure. Vá em frente e entre.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Depois de entrar, o PowerShell continuará. Depois de concluir feche o PowerShell e a configuração está concluída.

Nesse ponto, os serviços devem ser iniciados automaticamente e o agente estará agora monitorando e coletando dados. Lembre-se de que você verá avisos na janela do PowerShell caso não atenda a todos os pré-requisitos descritos nas seções anteriores. Não se esqueça de concluir os requisitos [aqui](active-directory-aadconnect-health.md#requirements) antes de instalar o agente. A seguinte captura de tela é um exemplo desses erros.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para verificar se o agente foi instalado, abra serviços e procure pelo seguinte. Esses serviços devem ser executados se você concluir a configuração. Caso contrário, eles não serão iniciados até que a configuração esteja concluída.

- Serviço de diagnóstico do AD FS do Azure AD Connect Health
- Serviço do Insights do AD FS do Azure AD Connect Health
- Serviço de Monitoramento do AD FS do Azure AD Connect Health
 
![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


## Instalação do agente em servidores do Windows Server 2008 R2

Para servidores Windows Server 2008 R2 faça o seguinte:

1. Certifique-se de que o servidor esteja em execução no Service Pack 1 ou superior.
1. Desative a ESC do IE para instalação do agente:
1. Instale o Windows PowerShell 4.0 em cada um dos servidores antes de instalar o agente de integridade do AD. Para instalar o Windows PowerShell 4.0:
 - Instale o [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) usando o link a seguir para baixar o instalador offline.
 - Instale o PowerShell ISE (de recursos do Windows)
 - Instale o [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Instale o Internet Explorer versão 10 ou superior no servidor. Isso é necessário para que o serviço de integridade faça sua autenticação usando suas credenciais de administrador do Azure.
1. Para obter informações adicionais sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, consulte o artigo no wiki [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

## Links relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->