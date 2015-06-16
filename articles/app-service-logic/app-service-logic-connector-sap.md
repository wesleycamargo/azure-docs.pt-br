<properties 
   pageTitle="Conector do SAP" 
   description="Como usar o Conector do SAP" 
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
   ms.date="03/20/2015"
   ms.author="hariag"/>


# Conector do SAP #

Conectores podem ser usados em aplicativos de lógicos para obter, processar ou enviar por push dados como parte de um fluxo. Há situações em que talvez você precise trabalhar com o SAP, que é instalado localmente e protegido pelo firewall. Utilizando o Conector do SAP em seu fluxo, você pode chegar a diversos resultados. Alguns exemplos:

1.	Expor uma seção dos dados residentes no seu SAP por meio de um front-end Web ou móvel do usuário.
2.	Publicar dados em seu SAP após a conclusão de processamento
3.	Extrair dados do SAP para uso em um processo comercial

Nesses cenários, é necessário fazer o seguinte:

1. Criar uma instância do Aplicativo de API do Conector do SAP
2. Estabelecer conectividade híbrida para o aplicativo de API se comunicar com o SAP local
3. Usar o aplicativo de API criado em um aplicativo lógico para obter o processo comercial desejado

## Criar uma instância do Aplicativo de API do Conector do SAP ##

Para usar o Conector do SQL, você precisa criar uma instância do Aplicativo de API para o “Conector do SAP”. Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção “+NOVO” na parte inferior esquerda do Portal do Azure.
2. Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise por "Conector do SAP".
3. Configure-o da seguinte maneira:
	1. Forneça os detalhes genéricos, como nome, plano de serviço de aplicativo e assim por diante na primeira folha
	2. Como parte das configurações do pacote, forneça as credenciais do SAP. Forneça também uma cadeia de conexão do Barramento de Serviço do Azure. Ela será usada para estabelecer a conectividade híbrida com o SAP local. 
	3. RFCs, TRFCs, BAPIs e IDOCs precisam ser configurados com base nas necessidades do cenário. Se for necessário fornecer vários valores, eles podem ser separados por vírgulas

![][1]

## Configurar o Aplicativo de API do Conector do SAP recém-criado ##

Navegue até o Aplicativo de API recém-criado, em Procurar -> Aplicativos de API -> <Name of the API App just created> e você verá o seguinte comportamento. A instalação está incompleta, pois a conexão híbrida ainda não foi estabelecida.

![][2]

Para estabelecer a conectividade híbrida, faça o seguinte:

1. Copie a cadeia de conexão principal
2. Clique no link “Baixar e configurar”
3. Siga o processo de instalação que é iniciado e forneça a cadeia de conexão principal quando for solicitado
4. Quando o processo de instalação for concluído, uma caixa de diálogo semelhante a esta será exibida

![][3]

Agora, quando navegar até o aplicativo de API, você observará que o status da conexão híbrida será Conectado.

![][4]

Observação: caso você queira trocar para a cadeia de conexão secundária, basta fazer novamente a configuração híbrida e fornecer a cadeia de conexão secundária em vez da cadeia principal

## Uso em um aplicativo lógico ##

O Conector do SAP pode ser usado como ação/etapa somente em um aplicativo lógico.

Ao criar/editar um aplicativo lógico, escolha o aplicativo de API do Conector do SAP criado acima. Isso listará todas as ações permitidas que podem ser escolhidas.

![][5]

Após a seleção de uma ação, ela listará os parâmetros de entrada da ação. Forneça os valores corretos e clique no ícone de escala.

![][6]

A etapa/ação aparecerá conforme configurado no aplicativo lógico. As saídas da operação serão mostradas e poderão ser usadas como entradas em uma etapa posterior.

![][7]

Conclua o aplicativo lógico para definir o processo comercial e execute-o para chegar ao objetivo desejado.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg



<!--HONumber=54--> 