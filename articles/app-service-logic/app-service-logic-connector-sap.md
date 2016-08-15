<properties
   pageTitle="Usando o conector SAP em Aplicativos lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o conector SAP ou o aplicativo de API e usá-lo em um Aplicativo lógico no Serviço de Aplicativo do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="sameerch"/>


# Começar a usar o conector SAP e adicione-o a seu Aplicativo Lógico

>[AZURE.NOTE] Com base nos comentários dos clientes, estamos atualizando esse conector. Atualizaremos esta página quando estiver pronto. Não recomendamos usar o conector do SAP antes que a nova versão esteja disponível. Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview dos Aplicativos Lógicos.

Conecte-se ao SAP local para chamar RFC ou obter metadados. Há situações em que talvez você precise trabalhar com o SAP, que é instalado localmente e protegido pelo firewall. Utilizando o conector SAP em seu fluxo, você pode chegar a diversos cenários. Alguns exemplos:

1.	Expor uma seção dos dados residentes no seu SAP por meio de um front-end Web ou móvel do usuário.
2.	Publicar dados em seu SAP após a conclusão de processamento
3.	Extrair dados do SAP para uso em um processo comercial

os conectores podem ser usados nos Aplicativos Lógicos para obter, processar ou enviar dados como parte de um fluxo. Você pode adicionar o conector SAP a seu fluxo de trabalho de negócios e processar os dados como parte desse fluxo dentro de um Aplicativo lógico.


Nesses cenários, é necessário fazer o seguinte:

1. Criar uma instância do Aplicativo de API do conector SAP
2. Estabelecer conectividade híbrida para o aplicativo de API se comunicar com o SAP local
3. Usar o Aplicativo de API criado em um Aplicativo lógico para conseguir o processo comercial desejado


## Criar uma instância do Aplicativo de API do conector SAP ##

Um conector pode ser criado em um Aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Procure "conector SAP", selecione-o e selecione **Criar**.
3. Configure-o da seguinte maneira:
	1. Forneça os detalhes genéricos, como nome, plano de serviço de aplicativo e assim por diante na primeira folha

	2. Como parte das configurações do pacote, forneça as credenciais do SAP. Forneça também uma cadeia de conexão do Barramento de Serviço do Azure. Ela será usada para estabelecer a conectividade híbrida com o SAP local.

	3. RFCs, TRFCs, BAPIs e IDOCs precisam ser configurados com base nas necessidades do cenário. Se for necessário fornecer vários valores, eles podem ser separados por vírgulas

![][1]

## Configurar o Aplicativo de API do conector SAP recém-criado ##

Navegue até o Aplicativo de API recém-criado, em Procurar -> Aplicativos de API -> <Nome do Aplicativo de API recém-criado> e você verá o comportamento a seguir. A instalação está incompleta pois a conexão híbrida ainda não foi estabelecida: ![][2]

O conector SAP exige uma conectividade híbrida para conectar *qualquer* ponto de extremidade SAP. Para estabelecer a conectividade híbrida, faça o seguinte:

1. Copie a cadeia de conexão principal
2. Clique no link “Baixar e configurar”
3. Siga o processo de instalação que é iniciado e forneça a cadeia de conexão principal quando for solicitado
4. Quando o processo de instalação for concluído, uma caixa de diálogo semelhante a esta será exibida: ![][3]

Mais detalhes sobre [como integrar um servidor SAP local](app-service-logic-integrate-with-an-on-premise-sap-server.md).

Agora, quando você procurar o Aplicativo de API novamente, observará que o status da conexão híbrida será Conectado: ![][4]

Observação: caso você queira trocar para a cadeia de conexão secundária, basta fazer novamente a configuração híbrida e fornecer a cadeia de conexão secundária em vez da cadeia principal

## Uso em um Aplicativo lógico ##

O conector SAP pode ser usado como uma ação/etapa somente em um Aplicativo lógico.

Ao criar/editar um Aplicativo lógico, escolha o Aplicativo de API do conector SAP criado acima. Isso listará todas as ações permitidas que podem ser escolhidas: ![][5]

Após a seleção de uma ação, ela listará os parâmetros de entrada da ação. Forneça os valores adequados e clique no ícone de Tique: ![][6]

Agora, a etapa/ação aparece conforme configurada no Aplicativo lógico. As saídas da operação serão mostradas e entradas poderão ser usadas em uma etapa posterior: ![][7]

Conclua o Aplicativo lógico para definir o processo comercial e execute-o para chegar ao objetivo desejado.

## Fazer mais com seu conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você quiser começar com os Aplicativos lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Aplicativo Lógico](https://tryappservice.azure.com/?appservice=logic), onde poderá criar imediatamente um Aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Confira [Gerenciar e Monitorar seus Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=AcomDC_0803_2016-->