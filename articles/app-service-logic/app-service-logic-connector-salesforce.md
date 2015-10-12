<properties
   pageTitle="Usando o Conector do Salesforce em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector do Salesforce ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
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


# Introdução ao Conector do Salesforce e à adição dele a seu Aplicativo Lógico
Conecte-se ao Salesforce e crie e modifique entidades como Contas, Clientes Potenciais e assim por diante. A seguir, os cenários de integração comuns que envolvem o Salesforce:

- A Sincronização de Conta entre o Salesforce e sistemas de ERP, como SAP e QuickBooks
- Fluxo da ordem ao caixa do Salesforce para sistemas de ERP

Como parte das configurações do pacote do conector, o usuário pode especificar entidades que o conector pode gerenciar e os parâmetros de ações, entrada e saída são preenchidos dinamicamente. A seguir, as diferentes ações disponíveis no conector do Salesforce:

- Criar Entidade - Use esta ação para criar uma nova entidade do Salesforce, como Conta, Caso ou um Objeto Personalizado.
- Atualizar Entidade - Use esta ação para atualizar uma entidade existente do Salesforce
- Inserir Entidade - Use esta ação para atualizar uma entidade existente do Salesforce ou criar uma, se ela não existir
- Excluir Entidade - Use esta ação para excluir uma entidade existente do Salesforce
- Executar Consulta - Use esta ação para executar uma consulta SELECT que é escrita em SOQL (Salesforce Object Query Language)

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o Conector do Salesforce a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.


## Criar um aplicativo de API do Conector do Salesforce
1.	Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.
2.	Navegue até “Web e Móvel > Aplicativos de API” e pesquise “Salesforce”.
3.	Configure o Conector do Salesforce fornecendo os detalhes do Plano de hospedagem e do grupo de recursos e selecionando o nome do Aplicativo de API: ![][15]
4. Configure as Entidades do Salesforce que você está interessado em ler/gravar nas “Configurações do Pacote”.

Assim, agora você pode criar um Aplicativo de API do Conector do Salesforce.


## Criar um aplicativo lógico
Vamos criar um aplicativo lógico simples que cria uma conta no Salesforce e atualiza os detalhes do endereço de cobrança da mesma conta.

1.	Faça logon no Portal do Azure e clique em ‘Novo -> Web + celular -> Aplicativo Lógico’: ![][1]

2.	Na página ‘Criar aplicativo lógico’, forneça os detalhes necessários, como nome, plano de serviço de aplicativo e local: ![][2]

3.	Clique em ‘Gatilhos e Ações’. O editor do Aplicativo Lógico será aberto: ![][3]

4.	Selecione ‘Executar esta lógica manualmente’, o que significa que o aplicativo lógico só poderá ser invocado manualmente: ![][4]

5.	Expanda “Aplicativos de API” neste grupo de recursos na Galeria para ver todos os aplicativos de API disponíveis. Selecione ‘Salesforce’ na galeria e o ‘conector do Salesforce’ é adicionado ao fluxo: ![][5]

8.	Para autorizar seu Aplicativo Lógico a acessar a conta do Salesforce, clique em “Autorizar” para fornecer as credenciais de logon do Salesforce: ![][6]

9.	Você será redirecionado à página de logon do Salesforce e poderá ser autenticado com suas credenciais do Salesforce: ![][7] ![][8]

10.	Quando a autorização for concluída, todas as ações serão exibidas: ![][9]

11.	Selecione a ação ‘Criar Conta’ e os parâmetros de entrada serão exibidos: ![][10]

12.	Forneça o ‘Nome da Conta’ e clique em ✓: ![][11]

13.	Selecione “Conector do Salesforce” na seção “Usados Recentemente” na galeria e uma nova ação do Salesforce será adicionada.

14.	Selecione ‘Atualizar Conta’ na lista de ações e os parâmetros de entrada da ação ‘Atualizar Conta’ serão exibidos: ![][12]

15.	Clique em ‘+’ ao lado de ‘Id do Registro’ para selecionar o valor da identificação na saída da ação ‘Criar Conta’: ![][13]

16.	Forneça valores para Rua da Cobrança, Cidade da Cobrança, Estado da Cobrança e Código Postal da Cobrança e clique em ✓: ![][14]

17. Clique em OK na tela do editor do aplicativo lógico e clique em “Criar”. Serão necessários cerca de 30 segundos para a conclusão da criação.

18. Procure pelo aplicativo lógico recém-criado e clique em “Executar” para iniciar uma execução.

19. Você pode verificar se uma nova conta com o nome ‘Contoso’ foi criada em sua conta do Salesforce.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST do Swagger em [Conectores e referência dos Aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png

<!---HONumber=Oct15_HO1-->