<properties 
	pageTitle="Integrar com um servidor SAP local no serviço de aplicativo do Microsoft Azure"
	description="Saiba como integrar um servidor SAP local"
	authors="rajeshramabathiran" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="harish"/>


# Integrar com um servidor SAP local
Usando o conector do SAP, você pode conectar aplicativos web, móveis e lógica de Serviços de Aplicativos do Azure ao servidor SAP existente.  Você pode invocar RFCs, BAPIs, tRFCs, bem como enviar IDOCs para o servidor SAP.
	
O servidor SAP ainda pode estar atrás firewall local.  No caso do servidor local, a conectividade é estabelecida por meio de um ouvinte híbrido, conforme mostrado:

![Hybrid connectivity flow][1]

Um conector de SAP na nuvem não pode se conectar diretamente a um servidor SAP atrás de um firewall.  O ouvinte híbrido preenche a lacuna hospedando um ponto de extremidade de retransmissão que permite ao conector estabelecer conectividade com o servidor SAP com segurança.


## Maneiras diferentes de integração com o SAP
Há suporte para as seguintes ações:

- Chamar RFC
- Chamar TRFC
- Chamar BAPI
- Enviar IDoc

## Pré-requisitos
As bibliotecas de cliente específicas do SAP são necessárias no computador cliente em que o ouvinte híbrido está instalado e em execução.  Os detalhes precisos são capturados [aqui][9] na seção denominada **Para o adaptador SAP**.


## Criar um novo adaptador SAP
1. Entre no Portal de Gerenciamento do Microsoft Azure. 
2. Selecione **Novo**.
3. Na folha de criação, selecione **Computação** > **Azure Marketplace**.
4. Na folha do marketplace, selecione **Aplicativos de API** e procure SAP na barra de pesquisa:
	
	![SAP Connector API App][2]	
5. Selecione o **Conector SAP** publicado pela Microsoft.
6. Na folha do conector SAP, selecione **Criar**.
7. Na nova folha que se abre, insira as seguintes informações:
	1. **Local** - escolha a região geográfica na qual você quer que o conector seja implantado
	2. **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	3. **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	4. **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
	5. **Camada de preços** - escolha uma camada de preços para o conector
	6. **Nome** - insira um nome para o conector SAP
	7. **Configurações do pacote**
		- **Nome do servidor** - insira o nome do servidor SAP.  Exemplo:  "SAPserver" ou "SAPserver.mydomain.com".
		- **Nome de usuário** - insira um nome de usuário válido para conectar-se ao servidor SAP.
		- **Senha** - digite uma senha válida para conectar-se ao servidor SAP.
		- **Número do sistema** - insira o número do sistema do servidor de aplicativos SAP.
		- **Idioma** - insira o idioma de logon, como "EN".  Se nenhum valor for inserido, "EN" é considerado.
		- **Local** - digite se o servidor SAP local atrás de um firewall ou não.  Se definido como TRUE, será necessário instalar um agente de escuta em um servidor que possa acessar o servidor SAP.  Você pode ir para a página de resumo do aplicativo de API e selecionar 'Hybrid Connection' para instalar o agente.
		- **Cadeia de conexão do barramento de serviço** - insira esse parâmetro se o servidor SAP for local.  Deve ser uma cadeia de conexão válida do Namespace do Barramento de Serviço.
		- **RFCs** - insira os RFCs no SAP que têm permissão para serem chamados pelo conector.
		- **TRFCs** - insira os TRFCs no SAP que têm permissão para serem chamados pelo conector.
		- **BAPI** - insira os BAPIs no SAP que têm permissão para serem chamados pelo conector.
		- **IDOCs** - insira os IDOCs no SAP que podem ser enviados pelo conector.
	8. Escolha Selecionar.  Em alguns minutos, seu conector SAP é criado.


## Instalar o ouvinte híbrido
Navegue até o conector SAP criado por meio de **Procurar** > **Aplicativos de API** > *nome do seu conector*

Na folha do conector, observe que o status da conexão híbrida é pendente.  Selecione a conexão híbrida.  A folha de Conexão Híbrida se abre:

![Hybrid connection blade][3]

Copie a cadeia de caracteres de configuração de gateway primária.  Você a utiliza posteriormente como parte da configuração de instalação do ouvinte híbrido.

Selecione o link **Baixar e configurar** e execute o instalador com um clique:

![Hybrid connection click once installer][4]

Selecione **instalar**e, em seguida, insira a configuração do gateway copiada anteriormente:

![Relay listen connection string][5]

Selecione **Instalar** para concluir a configuração do Gerenciador de conexão híbrida:

![Hybrid connection manager installation in progress][6]

![Hybrid connection manager installation completed][7]

## Validar a conexão híbrida
Navegue até o conector SAP criado por meio de **Procurar** > **Aplicativos de API** > *nome do seu conector*

Na folha do conector, observe que o status da conexão híbrida é *Connected*:

![Hybrid connection status - connected][8]


## Usando o conector SAP em aplicativos lógicos
Depois de criar o conector do SAP, ele pode ser usado dentro de seu fluxo de trabalho de aplicativos lógicos.

Crie um novo aplicativo lógico por meio de **Novo** > **Aplicativos Lógicos** > **Criar**.  Insira os metadados para o aplicativo lógico, incluindo o grupo de recursos.

Selecione **Gatilhos e ações**.  O designer de fluxo de trabalho de aplicativos lógica é aberto.

Selecione o conector SAP no painel direito e selecione uma ação na guia Ações. 

> [AZURE.NOTE] A lista de ações baseia-se na configuração que você inseriu quando criou o conector SAP. 

Para a ação selecionada, você verá os parâmetros de entrada e saída.  Você pode entrar nas entradas para a ação e usar a saída da ação atual em outros aplicativos de API, possivelmente para mais tomada de decisões no fluxo de trabalho.

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG	
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://download.microsoft.com/download/2/D/7/2D7CE8DF-A6C5-45F0-8319-14C3F1F9A0C7/InstallationGuide.htm




<!--HONumber=49-->