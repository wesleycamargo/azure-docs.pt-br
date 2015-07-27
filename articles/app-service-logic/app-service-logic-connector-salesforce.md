<properties
   pageTitle="Conector do Salesforce"
   description="Como usar o Conector do Salesforce"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


#Usando o Conector do Salesforce em seu aplicativo lógico#

Aplicativos lógicos podem ser disparados com base em uma variedade de fontes de dados e oferecem conectores para obter e processar dados como uma parte do fluxo. O conector permite criar e modificar entidades diferentes, como Contas, Clientes Potenciais etc. A seguir, os cenários de integração comum que envolvem o Salesforce.

- A Sincronização de Conta entre o Salesforce e sistemas de ERP, como SAP e QuickBooks

- Fluxo da ordem ao caixa do Salesforce para sistemas de ERP


Como parte das configurações do pacote do conector, o usuário pode especificar entidades que o conector pode gerenciar e os parâmetros de ações, entrada e saída são preenchidos dinamicamente. A seguir estão as diferentes ações disponíveis no Salesforce.

- Criar Entidade - Use esta ação para criar uma nova entidade do Salesforce, como Conta, Caso ou um Objeto Personalizado.

- Atualizar Entidade - Use esta ação para atualizar uma entidade existente do Salesforce

- Inserir Entidade - Use esta ação para atualizar uma entidade existente do Salesforce ou criar uma, se ela não existir

- Excluir Entidade - Use esta ação para excluir uma entidade existente do Salesforce

- Executar Consulta - Use esta ação para executar uma consulta SELECT que é escrita em SOQL (Salesforce Object Query Language)


##Criar um aplicativo de API do Conector do SalesForce##
1.	Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.
2.	Navegue até “Web e Dispositivos Móveis > Aplicativos de API” e procure por “SalesForce”.
3.	Configure o Conector do SalesForce fornecendo os detalhes do Plano de Hospedagem e o grupo de recursos e selecionando o nome do aplicativo de API.

	![][15]
4. Configure as Entidades do SalesForce que você está interessado em ler/gravar nas “Configurações do Pacote”.

Assim, agora você pode criar um Aplicativo de API do Conector do SalesForce.


##Criar um aplicativo lógico##
Vamos criar um aplicativo lógico simples que cria uma conta no Salesforce e atualiza os detalhes do endereço de cobrança da mesma conta.

1.	Faça logon no Portal do Azure e clique em “Novo -> Web + Móvel -> Aplicativo Lógico”

	![][1]

2.	Na página “Criar aplicativo lógico”, forneça os detalhes necessários, como nome, plano de serviço de aplicativo e local.

	![][2]

3.	Clique em “Gatilhos e Ações” e a tela do editor do Aplicativo Lógico aparecerá.

	![][3]

4.	Selecione “Executar esta lógica manualmente”, o que significa que o aplicativo lógico pode ser chamado apenas manualmente.

    ![][4]

5.	Expanda “Aplicativos de API” neste grupo de recursos na Galeria para ver todos os aplicativos de API disponíveis. Selecione “Salesforce” na galeria e o “Conector do Salesforce" é adicionado ao fluxo.


	![][5]

8.	Para autorizar seu aplicativo lógico a acessar a conta do SalesForce, clique em “Autorizar” para fornecer as credenciais de logon do SalesForce.

	![][6]

9.	Você será redirecionado à página de logon do Salesforce e poderá ser autenticado com suas credenciais do Salesforce.

	![][7]

	![][8]

10.	Quando a autorização for concluída, todas as ações serão exibidas.

	![][9]

11.	Selecione a ação “Criar Conta” e os parâmetros de entrada serão exibidos.

	![][10]

12.	Forneça o “Nome da Conta” e clique em ✓.

	![][11]

13.	Selecione “Conector do Salesforce” na seção “Usados Recentemente” na galeria e uma nova ação do Salesforce será adicionada.

14.	Selecione “Atualizar Conta” na lista de ações e os parâmetros de entrada da ação “Atualizar Conta” serão exibidos.

	![][12]

15.	Clique em “+” ao lado de “ID do Registro” para selecionar o valor da ID na saída da ação “Criar Conta”.

	![][13]

16.	Forneça valores para Rua da Cobrança, Cidade da Cobrança, Estado da Cobrança e CEP da Cobrança e clique em ✓.

	![][14]

17. Clique em OK na tela do editor do aplicativo lógico e clique em “Criar”. Serão necessários cerca de 30 segundos para a conclusão da criação.

18. Procure pelo aplicativo lógico recém-criado e clique em “Executar” para iniciar uma execução.

19. Você pode verificar se uma nova conta de nome foi criada em sua conta do Salesforce.

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

<!---HONumber=July15_HO3-->