<properties 
	pageTitle="Usar o ReportViewer em um site da Web | Microsoft Azure"
	description="Este tópico descreve como criar um site da Web do Microsoft Azure com o controle ReportViewer do Visual Studio que exibe um relatório armazenado em uma máquina virtual do Microsoft Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth" />

# Usar o ReportViewer em um site da Web hospedado no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de um recurso com o modelo clássico de implantação.

Você pode criar um site da Web do Microsoft Azure com o controle ReportViewer do Visual Studio que exibe um relatório armazenado em uma máquina virtual do Microsoft Azure. O controle ReportViewer é um aplicativo Web que você cria usando o modelo de aplicativo Web ASP.NET.

>[AZURE.IMPORTANT]Os modelos de aplicativo Web MVC ASP.NET não dão suporte ao controle ReportViewer.

Para incorporar o ReportViewer ao site da Web do Microsoft Azure, é necessário concluir as tarefas a seguir.

- **Adicionar** Assemblies ao Pacote de Implantação

- **Configurar** Autenticação e Autorização

- **Publicar** o aplicativo Web ASP.NET no Azure

## Pré-requisitos

Leia a seção "Recomendações gerais e práticas recomendadas" no [Business Intelligence do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-sql-server-business-intelligence.md).

>[AZURE.NOTE]Os controles ReportViewer são fornecidos com o Visual Studio Standard Edition ou versões posteriores. Se estiver usando o Web Developer Express Edition, você deverá instalar o [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) para usar os recursos de tempo de execução do ReportViewer.
>
>Não há suporte para o ReportViewer configurado em modo de processamento local no Microsoft Azure.

Leia o white paper [Controle do visualizador de relatórios do Reporting Services e servidores de relatórios com base na máquina virtual do Microsoft Azure](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## Adicionando assemblies ao pacote de implantação

Quando você hospeda o aplicativo ASP.NET no local, os assemblies do ReportViewer geralmente são instalados diretamente no GAC (cache de assembly global) do servidor IIS durante a instalação do Visual Studio e podem ser acessados diretamente pelo aplicativo. No entanto, ao hospedar o aplicativo ASP.NET na nuvem, o Microsoft Azure não permite que nada seja instalado no GAC, de modo que você deve verificar se os assemblies do ReportViewer estão disponíveis localmente para seu aplicativo. É possível fazer isso adicionando referências a eles no seu projeto e configurando-os para serem copiados localmente.

No modo de processamento remoto, o controle ReportViewer usa os seguintes assemblies:

- **Microsoft.ReportViewer.WebForms.dll**: contém o código do ReportViewer, que você precisa para usar o ReportViewer em sua página. Uma referência para esse assembly é adicionada ao seu projeto quando você solta um controle ReportViewer em uma página ASP.NET em seu projeto.

- **Microsoft.ReportViewer.Common.dll**: contém classes usadas pelo controle ReportViewer no tempo de execução. Ele não é adicionado automaticamente ao seu projeto.

### Para adicionar uma referência ao Microsoft.ReportViewer.Common

- Clique com o botão direito do mouse no nó **Referências** do projeto e selecione **Adicionar Referência**, selecione o assembly na guia .NET e clique em **OK**.

### Para tornar os assemblies acessíveis localmente ao aplicativo ASP.NET

1. Na pasta **Referências**, clique no assembly Microsoft.ReportViewer.Common para que suas propriedades apareçam no painel Propriedades.

1. No painel Propriedades, defina **Local da Cópia** como True.

1. Repita as etapas 1 e 2 para Microsoft.ReportViewer.WebForms.

### Para obter o pacote de idiomas do ReportViewer

1. Instale o pacote redistribuível do Microsoft Report Viewer 2012 Runtime apropriado por meio do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Selecione o idioma na lista suspensa e a página será redirecionada para a página do centro de download correspondente.

1. Clique em **Baixar** para iniciar o download do ReportViewerLP.exe.

1. Depois de baixar o ReportViewerLP.exe, clique em **Executar** para instalá-lo imediatamente ou clique em **Salvar** para salvá-lo no computador. Se você clicar em **Salvar**, lembre-se do nome da pasta em que você salvar o arquivo.

1. Localize a pasta onde você salvou o arquivo. Clique com o botão direito do mouse em ReportViewerLP.exe, clique em **Executar como administrador** e em **Sim**.

1. Depois de executar ReportViewerLP.exe, você verá que em c:\\windows\\assembly estão os arquivos de recursos **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources**.

### Para configurar o controle ReportViewer localizado

1. Baixe e instale o pacote redistribuível do Microsoft Report Viewer 2012 Runtime seguindo as instruções especificadas acima.

1. Crie a pasta <language> no projeto e copie nela os arquivos de assembly do recurso associado. Os arquivos de assembly do recurso a serem copiados são: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selecione os arquivos de assembly do recurso e, no painel Propriedades, defina **Copiar para Diretório de Saída** como "**Copiar sempre**".

1. Defina Culture e UICulture para o projeto Web. Para obter mais informações sobre como definir Cultura e Cultura de interface de usuário para uma página da Web ASP.NET, consulte [Como definir Cultura e Cultura de interface de usuário para globalização da página da Web ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## Configurando a autenticação e a autorização

O ReportViewer precisa usar credenciais apropriadas para se autenticar no servidor de relatório, e as credenciais devem ser autorizadas pelo servidor de relatório a acessar os relatórios que você deseja. Para obter informações sobre autenticação, consulte o white paper [Controle do visualizador de relatórios do Reporting Services e servidores de relatórios com base em máquina virtual do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## Publicar o aplicativo Web ASP.NET no Azure

Para obter instruções sobre como publicar um aplicativo Web ASP.NET no Azure, consulte [Como Migrar e Publicar um Aplicativo Web no Azure do Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx) e [Introdução aos aplicativos Web e ao ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT]Se o comando Adicionar Projeto de Implantação do Azure ou Adicionar Projeto de Serviço de Nuvem do Azure não aparecer no menu de atalho do Gerenciador de Soluções, talvez seja necessário alterar a estrutura de destino do projeto para o .NET Framework 4.
>
>Os dois comandos fornecem basicamente a mesma funcionalidade. Um ou outro comando aparecerá no menu de atalho de acordo com a versão do Microsoft Azure SDK que você instalou.

## Recursos

[Relatórios da Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[Business Intelligence do SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-business-intelligence.md)

[Use o PowerShell para criar uma VM do Azure com um servidor de relatório em modo nativo](virtual-machines-sql-server-create-native-mode-report-server-powershell.md)

[Controle de visualizador de relatórios do Reporting Services e máquina virtual do Microsoft Azure com base em servidores de relatórios](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)

<!---HONumber=Sept15_HO4-->