<properties
	pageTitle="Integrar com um servidor SAP local no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como integrar um servidor SAP local"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="sameerch"/>


# Integrar com um servidor SAP local

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview dos Aplicativos Lógicos.

Usando o [conector SAP](app-service-logic-connector-sap.md), você pode conectar os aplicativos Web, móveis e lógicos dos Serviços de Aplicativo do Azure ao servidor SAP existente. Isso permite a invocação de RFCs, BAPIs, tRFCs, além do envio de IDocs ao servidor SAP, mesmo se ele estiver atrás de seu firewall local.

Se você tiver um servidor SAP local, use um ouvinte híbrido para estabelecer a conectividade com o conector SAP, conforme exibido:

![Fluxo de conectividade híbrida][1]

Embora um conector SAP na nuvem não possa conectar-se diretamente a um servidor SAP protegido por um firewall local, você pode usar o ouvinte Híbrido para compensar o espaço. Faça isso hospedando um ponto de extremidade de retransmissão que permite ao conector estabelecer conectividade com o servidor SAP com segurança.


## Maneiras diferentes de integração com o SAP
Há suporte para as seguintes ações:

- Chamar RFC
- Chamar tRFC
- Chamar BAPI
- Enviar IDoc

## Pré-requisitos
As bibliotecas de cliente específicas do SAP são necessárias no computador cliente em que o ouvinte híbrido está instalado e em execução. Os detalhes precisos estão no [Guia de Instalação do Pacote do Adaptador BizTalk][9] na seção denominada **Para o adaptador SAP**.


## Criar um novo conector SAP
1. Entre no Portal do Azure.
2. Selecione **Novo**.
3. Na folha de criação, selecione **Computação** > **Azure Marketplace**.
4. Na folha do Marketplace, selecione **Aplicativos de API** e procure SAP na barra de pesquisa: ![Aplicativo de API do Conector SAP][2]
5. Selecione o **conector SAP** publicado pela Microsoft.
6. Na folha do conector SAP, selecione **Criar**.
7. Na nova folha que se abre, insira as seguintes informações:
	1. **Local** - escolha a região geográfica na qual você quer que o conector seja implantado
	2. **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	3. **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	4. **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
	5. **Camada de preços** - escolha uma camada de preços para o conector
	6. **Nome** - insira um nome para o conector SAP
	7. **Configurações do pacote**
		- **Nome do servidor** - insira o nome do servidor SAP. Exemplo: "SAPserver" ou "SAPserver.mydomain.com".
		- **Nome de usuário** - insira um nome de usuário válido para conectar-se ao servidor SAP.
		- **Senha** - digite uma senha válida para conectar-se ao servidor SAP.
		- **Número do sistema** - insira o número do sistema do servidor de aplicativos SAP.
		- **Idioma** - insira o idioma de logon, como "EN". Se nenhum valor for inserido, "EN" é considerado.
		- **Local** - digite se o servidor SAP é local e está atrás de um firewall ou não. Se definido como TRUE, será necessário instalar um agente de escuta em um servidor que possa acessar o servidor SAP. Você pode ir para a página de resumo do aplicativo de API e selecionar “Conexão Híbrida” para instalar o agente.
		- **Cadeia de conexão do barramento de serviço** - insira esse parâmetro se o servidor SAP for local. Deve ser uma cadeia de conexão válida do Namespace do Barramento de Serviço.
		- **RFCs** - insira os RFCs no SAP que têm permissão para serem chamados pelo conector.
		- **TRFCs** - insira os TRFCs no SAP que têm permissão para serem chamados pelo conector.
		- **BAPI** - insira os BAPIs no SAP que têm permissão para serem chamados pelo conector.
		- **IDOCs** - insira no SAP os IDOCs que podem ser enviados pelo conector.
	8. Escolha Selecionar. Em alguns minutos, seu conector SAP é criado.


## Instalar o ouvinte híbrido
Navegue até o conector SAP criado: **Procurar** > **Aplicativos de API** > *nome do seu conector*

Na folha do conector, observe que o status da conexão híbrida é pendente. Selecione a conexão híbrida. A folha de Conexão Híbrida se abre:

![Folha de conexão híbrida][3]

Copie a cadeia de caracteres de configuração de gateway primária. Você a usará posteriormente como parte da configuração do ouvinte híbrido.

Selecione o link **Baixar e configurar**. O instalador de um clique é aberto:

![Instalador com um clique de conexão híbrida][4]

Selecione **Instalar** e insira a cadeia de caracteres de configuração de gateway primário copiada anteriormente:

![Retransmitir cadeia de conexão de escuta][5]

Selecione **Instalar** para concluir a configuração do Gerenciador de conexão híbrida:

![Instalação do gerenciador de conexão híbrida em andamento][6]

![Instalação do gerenciador de conexão híbrida concluída][7]

## Validar a conexão híbrida
Navegue até o conector SAP criado: **Procurar** > **Aplicativos de API** > *nome do seu conector*

Na folha do conector, observe que o status da conexão híbrida é *Conectado*:

![Status da conexão híbrida - conectada][8]


## Usando o conector SAP nos Aplicativos lógicos
Depois de criar o conector SAP, ele poderá ser usado dentro do fluxo de trabalho de seus Aplicativos lógicos. Para tanto, crie um novo Aplicativo lógico em **Novo** > **Aplicativos lógicos** > **Criar**. Insira os metadados para o Aplicativo lógico, incluindo o grupo de recursos.

Selecione **Disparadores e ações**. O designer do fluxo de trabalho dos Aplicativos lógicos é aberto.

Selecione o conector SAP no painel direito e selecione uma ação na guia Ações.

> [AZURE.NOTE] A lista de ações baseia-se na configuração que você inseriu quando criou o conector SAP.

Para a ação selecionada, você verá os parâmetros de entrada e saída. Você pode entrar nas entradas para a ação e usar a saída da ação atual em outros aplicativos de API, possivelmente para mais tomada de decisões no fluxo de trabalho.

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://www.microsoft.com/download/details.aspx?id=35552

<!---HONumber=AcomDC_0727_2016-->