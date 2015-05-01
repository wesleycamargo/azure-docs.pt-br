<properties 
   pageTitle="Conector do SugarCRM" 
   description="Como usar o Conector do SugarCRM" 
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
   ms.date="04/01/2015"
   ms.author="vagarw"/>


#Usando o Conector do SugarCRM em seu aplicativo lógico#

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. O Conector do SugarCRM permite criar e modificar entidades diferentes, como contas, clientes potenciais, contatos etc. A seguir estão os cenários de integração típicos que envolvem o SugarCRM.

- Sincronização de contas entre sistemas de ERP e SugarCRM, como SAP

- Sincronização de contas, contatos e clientes potenciais entre Marketo e SugarCRM

- Fluxo da ordem ao caixa do SugarCRM para sistemas de ERP


Como parte das configurações do pacote do conector, o usuário pode especificar entidades que pode o conector pode gerenciar e os parâmetros de ações, entrada e saída são preenchidos dinamicamente. 

##Ações do Conector do SugarCRM##
A seguir estão as diferentes ações disponíveis no Conector do SugarCRM.
 
- Criar Módulo - use essa ação para criar um novo registro para o módulo do SugarCRM, como Contas, Clientes Potenciais e Contatos.

- Atualizar Módulo - use essa ação para atualizar um registro existente para o módulo do SugarCRM.

- Excluir Módulo - use essa ação para excluir um registro existente do módulo do SugarCRM.

- Listar Módulo - use essa ação para listar registros filtrados. Se nenhuma consulta for especificada, todos os registros serão retornados.

- Obter Módulo - use essa ação para recuperar um único registro do módulo especificado.

- Obter a Contagem de Registros - use essa ação para obter o número de registros no módulo que correspondem à consulta. Se nenhuma consulta for especificada, o número total de registros no módulo será retornado. 

- Verificar Módulos Duplicados - use essa ação para verificar se há registros duplicados em um módulo.

*Observação*: para obter mais detalhes sobre os argumentos com suporte na consulta, consulte a documentação da API REST do SugarCRM.
   
##Criar um aplicativo de API do Conector do SugarCRM##
1.	Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "SugarCRM".
3.	Configure o Conector do SugarCRM fornecendo os detalhes do Plano de Hospedagem e o grupo de recursos e selecionando o nome do Aplicativo de API.

4. Configure o conector do SugarCRM e clique em Criar. A seguir estão as configurações de pacote que você precisa fornecer para criar o conector:

	<table>
	  <tr>
	    <td><b>Nome</b></td>
	    <td><b>Obrigatório</b></td>
	    <td><b>Descrição</b></td>
	  </tr>
	  <tr>
	    <td>URL do Site</td>
	    <td>Sim</td>
	    <td>Especifique a URL de sua instância do SugarCRM, por ex.: https://abcde1234.sugarcrm.com</td>
	  </tr>
	  <tr>
	    <td>Id do Cliente</td>
	    <td>Sim</td>
	    <td>Especifique a chave do consumidor da chave oauth 2.0 no SugarCRM </td>
	  </tr>
	  <tr>
	    <td>Segredo do Cliente</td>
	    <td>Sim</td>
	    <td>Especifique p segredo do consumidor da chave oauth 2.0 no SugarCRM </td>
	  </tr>
	<tr>
	    <td>Nome de Usuário</td>
	    <td>Sim</td>
	    <td>Especifique o nome do usuário do SugarCRM</td>
	  </tr>
		<tr>
	    <td>Senha</td>
	    <td>Sim</td>
	    <td>Especifique a senha do usuário do SugarCRM</td>
	  </tr>
	  <tr>
	    <td>Nomes de Módulo</td>
	    <td>Sim</td>
	    <td>Especifique os módulos do SugarCRM, como Contas, Contatos, Produtos etc., em que você deseja realizar a operação<br><br>Ex: Contas, Clientes Potenciais, Contatos</td>
	  </tr>
	</table>

	![][9]				



##Criar um aplicativo lógico##
Vamos criar um aplicativo lógico simples que cria uma conta no SugarCRM e atualiza os detalhes do endereço de cobrança da mesma conta.

1.	Faça logon no Portal do Azure e clique em 'Novo -> Web + Móvel -> Aplicativo Lógico'

	![][1]

2.	Na página 'Criar aplicativo lógico', forneça os detalhes necessários, como nome, plano de serviço de aplicativo e local.

	![][2]

3.	Clique em 'Gatilhos e Ações', e a tela do editor de Aplicativos Lógicos aparecerá. Selecione 'Executar esta lógica manualmente', o que significa que o aplicativo lógico pode ser invocado apenas manualmente.


5.	Expanda 'Aplicativos de API neste grupo de recursos' na Galeria para ver todos os aplicativos de API disponíveis. Selecione 'SugarCRM' na galeria, e o 'Conector do SugarCRM' será adicionado ao fluxo.


	![][3]

6.	Selecione a ação 'Criar Conta', e os parâmetros de entrada serão exibidos.

	![][4]

12.	Forneça um nome como 'Conta da Microsoft' e clique em ✓. 

	![][5]

13.	Selecione 'Conector do SugarCRM' na seção 'Usados Recentemente' na galeria, e uma nova ação do SugarCRM será adicionada.

14.	Selecione 'Atualizar Conta' na lista de ações, e os parâmetros de entrada da ação 'Atualizar Conta' serão exibidos.

	![][6]

15.	Clique em '+' ao lado de 'Id do Registro' para selecionar o valor da saída da ação 'Criar Conta'. 

	![][7]

16.	Forneça valores para as informações de endereço de cobrança e clique em ✓.

	![][8]

17. Clique em OK na tela do editor de aplicativo lógico e clique em 'Criar'. Serão necessários cerca de 30 segundos para a conclusão da criação.

18. Procure pelo aplicativo lógico recém-criado e clique em 'Executar' para iniciar uma execução.

19. Você pode verificar se uma nova conta com o nome 'Conta da Microsoft' foi criada em sua conta do SugarCRM e se a mesma conta também foi atualizada com as informações do endereço de cobrança.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png




<!--HONumber=52-->