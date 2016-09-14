<properties 
	pageTitle="Migrar um aplicativo Web corporativo para o Serviço de Aplicativo do Azure" 
	description="Mostra como usar o Assistente de migração de aplicativos Web para migrar rapidamente sites existentes do IIS para aplicativos Web do Serviço de Aplicativo do Azure" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2016" 
	ms.author="cephalin"/>

# Migrar um aplicativo Web corporativo para o Serviço de Aplicativo do Azure

Você pode migrar facilmente seus sites existentes executados no IIS (Serviço de Informação de Internet) 6 ou posterior para [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

>[AZURE.IMPORTANT] O Windows Server 2003 chegou ao fim do suporte em 14 de julho de 2015. Se atualmente você estiver hospedando sites em um servidor IIS que é o Windows Server 2003, o uso de Aplicativos Web é um modo de baixo risco, baixo custo e baixo atrito para manter seus sites online, e o Assistente de migração de aplicativos Web pode ajudar a automatizar o processo de migração para você.

O [Assistente de migração de aplicativos Web](https://www.movemetothecloud.net/) pode analisar a instalação do seu servidor IIS, identificar quais sites podem ser migrados para o Serviço de Aplicativo, realçar quaisquer elementos que não possam ser migrados ou aos quais a plataforma não dá suporte e, em seguida, migrar seus sites e bancos de dados associados para o Azure.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Elementos verificados durante a análise de compatibilidade ##
O Assistente de migração cria um relatório de prontidão para identificar quaisquer potenciais causas de preocupação ou problemas de bloqueio que podem impedir uma migração bem-sucedida do IIS local para aplicativos Web do Serviço de Aplicativo do Azure. Alguns dos principais itens aos quais você deve se atentar:

-	Associações de porta – aplicativos Web somente dão suporte à porta 80 para HTTP e à porta 443 para tráfego HTTPS. Configurações de porta diferentes serão ignoradas e o tráfego será roteado para 80 ou 443.
-	Autenticação – os aplicativos Web dão suporte à Autenticação Anônima por padrão e à Autenticação de Formulários onde especificado por um aplicativo. A autenticação do Windows pode ser usada somente com a integração com o Active Directory do Azure e o ADFS. Não há suporte no momento para nenhuma outra forma de autenticação - por exemplo, Autenticação Básica.
-	Cache de Assembly Global (GAC) – os aplicativos Web não dão suporte ao GAC. Se seu aplicativo faz referência a conjuntos que você normalmente implanta no GAC, você precisará implantar na pasta bin do aplicativo em aplicativos Web.
-	Modo de compatibilidade IIS5 – não há suporte para esse modo nos aplicativos Web.
-	Pools de aplicativos – em aplicativos Web, cada site e seus aplicativos filho são executados no mesmo pool de aplicativos. Se o site tiver vários aplicativos filho utilizando vários pools de aplicativos, consolide-os em um único pool de aplicativos com as mesmas configurações ou migre cada aplicativo em um aplicativo Web separado.
-	Componentes COM – aplicativos Web não permitem o registro de componentes COM na plataforma. Se seus sites ou aplicativos utilizarem componentes COM, você deve reescrevê-los em código gerenciado e implantá-los em seu site ou aplicativo.
-	Filtros ISAPI – os aplicativos Web podem dar suporte ao uso de filtros ISAPI. Você precisa fazer o seguinte:
	-	implantar as DLLs com seu aplicativo Web
	-	registrar as DLLs usando [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
	-	Coloque um arquivo applicationHost.xdt na raiz do site com o conteúdo seguinte:

			<?xml version="1.0"?>
			<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
			<configSections>
			    <sectionGroup name="system.webServer">
			      <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
			    </sectionGroup>
			  </configSections>
			</configuration>

		Para obter mais exemplos de como usar transformações de documentos XML com o seu site, consulte [Transformar seu Site do Microsoft Azure](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-	Outros componentes, como SharePoint, extensões de servidor do FrontPage (FPSE), FTP, certificados SSL não serão migrados.

## Como usar o Assistente de migração de aplicativos Web ##
Esta seção percorre um exemplo para migrar alguns sites que usam um banco de dados do SQL Server e em execução em um computador Windows Server 2003 R2 (IIS 6.0) no local:

1.	No servidor IIS ou em seu computador cliente, navegue até [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/)

	![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.	Instalar o Assistente de migração de aplicativos Web, clicando no botão **Servidor IIS dedicado**. Mais opções serão disponibilizadas futuramente.
4.	Clique no botão **Instalar Ferramenta** para instalar o Assistente de migração de aplicativos Web em seu computador.

	![](./media/web-sites-migration-from-iis-server/install-page.png)

	>[AZURE.NOTE] Você também pode clicar em **Download para instalação offline** para baixar um arquivo ZIP para instalação em servidores não conectados à Internet. Ou, você pode clicar em **Carregar um relatório de preparação de migração existente**, que é uma opção avançada para trabalhar com um migração de preparação para o relatório existente que você gerou anteriormente (explicado posteriormente).

5.	Na tela **Instalação do aplicativo**, clique em **Instalar** para instalar em seu computador. Ele também instalará dependências correspondentes, como a implantação da Web, DacFX e IIS, se necessário.

	![](./media/web-sites-migration-from-iis-server/install-progress.png)

	Uma vez instalado, o Assistente de migração de aplicativos Web inicia automaticamente.
  
6.	Escolha **Migrar sites e bancos de dados de um servidor remoto para o Azure**. Insira as credenciais administrativas para o servidor remoto e clique em **Continuar**.

	![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

	Você certamente pode optar por migrar do servidor local. A opção remota é útil quando você deseja migrar sites de um servidor IIS de produção.
 
	Agora a ferramenta de migração inspecionará a configuração do seu servidor IIS, como sites, aplicativos, pools de aplicativos e dependências para identificar sites candidatos para a migração.

8.	A captura de tela abaixo mostra três sites – **site padrão**, **TimeTracker** e **CommerceNet4**. Todos eles têm um banco de dados associado que desejamos migrar. Selecione todos os sites que você gostaria de avaliar e, em seguida, clique em **Avançar**.

	![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.	Clique em **Carregar** para carregar o relatório de preparação. Se clicar em **Salvar arquivo localmente**, você poderá executar a ferramenta de migração novamente mais tarde e carregar o relatório de prontidão salvo como observado anteriormente.

	![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
	Depois que você carregar o relatório de preparação, o Azure executa análise de preparação e mostra os resultados. Leia os detalhes de avaliação para cada site e certifique-se de que você compreenda ou que tenha resolvido todos os problemas antes de continuar.
 
	![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12.	Clique em **Começar a migração** para iniciar a migração. Você será redirecionado para o Azure para fazer logon em sua conta. É importante que você faça logon com uma conta que tenha uma assinatura ativa do Azure. Se não tiver uma conta do Azure, é possível se inscrever para obter uma avaliação gratuita [aqui](https://azure.microsoft.com/pricing/free-trial/?WT.srch=1&WT.mc_ID=SEM_).

13.	Selecione a conta de locatário, a assinatura do Azure e a região a usar para seus bancos de dados e aplicativos Web do Azure migrados; então, clique em **Iniciar Migração**. Você pode selecionar os sites para migrar posteriormente.

	![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14.	Na próxima tela, você pode fazer alterações nas configurações de migração padrão, como:

	- usar um banco de dados SQL Azure existente ou criar um novo banco de dados SQL do Azure e configurar suas credenciais
	- Selecione os sites para migrar
	- defina nomes para os aplicativos Web do Azure e seus bancos de dados SQL vinculados
	- personalize as configurações globais e configurações no nível do site

	A captura de tela abaixo mostra todos os sites selecionados para migração com as configurações padrão.

	![](./media/web-sites-migration-from-iis-server/migration-settings.png)

	>[AZURE.NOTE] A caixa de seleção **Habilitar o Active Directory do Azure** em configurações personalizadas integra o site ao [Active Directory do Azure](active-directory-whatis.md) (o **diretório padrão**). Para obter mais informações sobre sincronização do Active Directory do Azure com o Active Directory no local, consulte [Integração de diretórios](http://msdn.microsoft.com/library/jj573653).

16.	 Após fazer as alterações desejadas, clique em **Criar** para iniciar o processo de migração. A ferramenta de migração criará o banco de dados SQL do Azure e o aplicativo Web do Azure e, em seguida, publicará o conteúdo do site e os bancos de dados. O andamento da migração é mostrado com clareza na ferramenta de migração e você verá, no final, uma tela de resumo que detalha os sites migrados, se eles foram ou não bem-sucedidos e que vincula aos aplicativos Web do Azure recém-criados.

	Se ocorrer algum erro durante a migração, a ferramenta de migração claramente indicará a falha e reverterá as alterações. Você também poderá enviar o relatório de erros diretamente à equipe de engenharia, clicando no botão **Enviar relatório de erros**, com a pilha de chamadas de falhas capturadas e criar o corpo da mensagem.

	![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

	Se a migração tiver êxito e não resultar em erros, você também pode clicar no botão **Enviar Comentários** para fornecer comentários diretamente.
 
20.	Clique nos links para os aplicativos Web do Azure e verifique se a migração foi bem-sucedida.

21. Agora você pode gerenciar os aplicativos Web migrados no Serviço de Aplicativo do Azure. Para fazer isso, faça logon no [Portal do Azure](https://portal.azure.com).

22. No Portal do Azure, abra a folha Aplicativos Web para ver seus sites migrados (mostrados como aplicativos Web), clique em qualquer um para começar a gerenciar o aplicativo Web, como a configuração de publicação contínua, criação de backups, dimensionamento automático e monitoramento de uso ou de desempenho.

	![](./media/web-sites-migration-from-iis-server/TimeTrackerMigrated.png)

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
 

<!---HONumber=AcomDC_0831_2016-->