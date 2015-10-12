<properties
   pageTitle="Usando o Conector SAP em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector SAP ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Comece a usar o Conector SAP e adicione-o a seu Aplicativo Lógico
Conecte-se ao SAP local para chamar RFC ou obter metadados. Há situações em que talvez você precise trabalhar com o SAP, que é instalado localmente e protegido pelo firewall. Utilizando o Conector do SAP em seu fluxo, você pode chegar a diversos resultados. Alguns exemplos:

1.	Expor uma seção dos dados residentes no seu SAP por meio de um front-end Web ou móvel do usuário.
2.	Publicar dados em seu SAP após a conclusão de processamento
3.	Extrair dados do SAP para uso em um processo comercial

Conectores podem ser usados em aplicativos de lógicos para obter, processar ou enviar dados como parte de um fluxo. Você pode adicionar o Conector SAP a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.


Nesses cenários, é necessário fazer o seguinte:

1. Criar uma instância do Aplicativo de API do Conector do SAP
2. Estabelecer conectividade híbrida para o aplicativo de API se comunicar com o SAP local
3. Usar o aplicativo de API criado em um aplicativo lógico para obter o processo comercial desejado


## Criar uma instância do Aplicativo de API do Conector do SAP ##

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Procure "Conector SAP", selecione-o e selecione **Criar**.
3. Configure-o da seguinte maneira:
	1. Forneça os detalhes genéricos, como nome, plano de serviço de aplicativo e assim por diante na primeira folha

	2. Como parte das configurações do pacote, forneça as credenciais do SAP. Forneça também uma cadeia de conexão do Barramento de Serviço do Azure. Ela será usada para estabelecer a conectividade híbrida com o SAP local.

	3. RFCs, TRFCs, BAPIs e IDOCs precisam ser configurados com base nas necessidades do cenário. Se for necessário fornecer vários valores, eles podem ser separados por vírgulas

![][1]

## Configurar o Aplicativo de API do Conector do SAP recém-criado ##

Navegue até o Aplicativo de API recém-criado, em Procurar -> Aplicativos de API -> <Name of the API App just created> e você verá o comportamento a seguir. A instalação está incompleta pois a conexão híbrida ainda não foi estabelecida: ![][2]

O Conector do SAP exige conectividade híbrida para se conectar a *qualquer* ponto de extremidade do SAP. Para estabelecer a conectividade híbrida, faça o seguinte:

1. Copie a cadeia de conexão principal
2. Clique no link “Baixar e configurar”
3. Siga o processo de instalação que é iniciado e forneça a cadeia de conexão principal quando for solicitado
4. Quando o processo de instalação for concluído, uma caixa de diálogo semelhante a esta será exibida: ![][3]

Agora, quando você procurar o Aplicativo de API novamente, observará que o status da conexão híbrida será Conectado: ![][4]

Observação: caso você queira trocar para a cadeia de conexão secundária, basta fazer novamente a configuração híbrida e fornecer a cadeia de conexão secundária em vez da cadeia principal

## Uso em um aplicativo lógico ##

O Conector do SAP pode ser usado como ação/etapa somente em um aplicativo lógico.

Ao criar/editar um aplicativo lógico, escolha o aplicativo de API do Conector do SAP criado acima. Isso listará todas as ações permitidas que podem ser escolhidas: ![][5]

Após a seleção de uma ação, ela listará os parâmetros de entrada da ação. Forneça os valores adequados e clique no ícone de Tique: ![][6]

A etapa/ação agora aparece conforme configurada no aplicativo lógico. As saídas da operação serão mostradas e entradas poderão ser usadas em uma etapa posterior: ![][7]

Conclua o aplicativo lógico para definir o processo comercial e execute-o para chegar ao objetivo desejado.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST do Swagger em [Conectores e referência dos Aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=Oct15_HO1-->