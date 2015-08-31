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
   ms.date="08/19/2015"
   ms.author="sameerch"/>


# Usando o Conector do SugarCRM em seu aplicativo lógico

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. O conector do SugarCRM permite criar e modificar entidades diferentes, como Contas, Clientes Potenciais, Contatos, etc. A seguir, os cenários de integração comum que envolvem o SugarCRM.

- Sincronização de contas entre sistemas de ERP e SugarCRM, como SAP

- Sincronização de contas, contatos e clientes potenciais entre Marketo e SugarCRM

- Fluxo da ordem ao caixa do SugarCRM para sistemas de ERP


Como parte das configurações do pacote do conector, o usuário pode especificar entidades que o conector pode gerenciar e os parâmetros de ações, entrada e saída são preenchidos dinamicamente.

## Ações do Conector do SugarCRM
A seguir estão as diferentes ações disponíveis no Conector do SugarCRM.

- Criar Módulo - use essa ação para criar um novo registro para o módulo do SugarCRM, como Contas, Clientes Potenciais e Contatos.

- Atualizar Módulo - use essa ação para atualizar um registro existente para o módulo do SugarCRM.

- Excluir Módulo - use essa ação para excluir um registro existente do módulo do SugarCRM.

- Listar Módulo - use essa ação para listar registros filtrados. Se nenhuma consulta for especificada, todos os registros serão retornados.

- Obter Módulo - use essa ação para recuperar um único registro do módulo especificado.

- Obter a Contagem de Registros - use essa ação para obter o número de registros no módulo que correspondem à consulta. Se nenhuma consulta for especificada, o número total de registros no módulo será retornado.

- Verificar Módulos Duplicados - use essa ação para verificar se há registros duplicados em um módulo.

*Observação*: para obter mais detalhes sobre os argumentos com suporte em uma consulta, consulte a documentação da [API REST do SugarCRM](https://msdn.microsoft.com/library/dn705870).

## Criar um aplicativo de API do Conector do SugarCRM
1.	Navegue até portal.azure.com. Abra o Azure Marketplace usando a opção +NOVO na parte superior esquerda do Portal do Azure.
2.	Vá para "Marketplace > Tudo" e procure "SugarCRM".
3.	Configure o Conector do SugarCRM fornecendo os detalhes do Plano de Serviço de Aplicativo e o grupo de recursos inserindo o nome do Aplicativo de API.
4. Defina as configurações do pacote do Conector do SugarCRM. A seguir estão as configurações de pacote que você precisa fornecer para criar o conector:

	Nome | Obrigatório | Descrição
--- | --- | ---
URL do site | Sim | Insira a URL de sua instância do SugarCRM. Por exemplo, insira https://abcde1234.sugarcrm.com.
Id do Cliente | Sim | Insira a chave do consumidor da chave OAUTH 2.0 no SugarCRM. 
Segredo do Cliente | Sim | Digite o segredo do consumidor do OAUTH.
Nome de Usuário | Sim | Digite o nome do usuário do SugarCRM.
Senha | Sim | DIgite a senha do usuário do SugarCRM.
Nomes de Módulo | Sim | Insira os módulos do SugarCRM (como Contas, Contatos, Produtos) em que você deseja realizar a operação<br><br>Por exemplo: Contas, Clientes Potenciais, Contatos  
  
![][9]



## Criar um aplicativo lógico
Vamos criar um aplicativo lógico simples que cria uma conta no SugarCRM e atualiza os detalhes do endereço de cobrança da mesma conta.

1.	Faça logon no Portal do Azure e clique em “Novo -> Web + Móvel -> Aplicativo Lógico”

	![][1]

2.	Na página “Criar aplicativo lógico”, forneça os detalhes necessários, como nome, plano de serviço de aplicativo e local.

	![][2]

3.	Clique em “Gatilhos e Ações” e a tela do editor do Aplicativo Lógico aparecerá. Selecione “Executar esta lógica manualmente”, o que significa que o aplicativo lógico pode ser chamado apenas manualmente.


5.	Expanda “Aplicativos de API” neste grupo de recursos na Galeria para ver todos os aplicativos de API disponíveis. Selecione “SugarCRM” na galeria, e o “Conector do SugarCRM” será adicionado ao fluxo.


	![][3]

6.	Selecione a ação “Criar Conta” e os parâmetros de entrada serão exibidos.

	![][4]

12.	Forneça um nome como “Conta da Microsoft” e clique em ✓.

	![][5]

13.	Selecione “Conector do SugarCRM” na seção “Usados Recentemente” na galeria, e uma nova ação do SugarCRM será adicionada.

14.	Selecione “Atualizar Conta” (em “...” nas ações avançadas) na lista de ações e os parâmetros de entrada da ação “Atualizar Conta” serão exibidos.

	![][6]

15.	Clique em “...” ao lado de “ID do Registro” para selecionar o valor da “ID” na saída da ação “Criar Conta”.

	![][7]

16.	Forneça valores para as informações de endereço de cobrança e clique em ✓.

	![][8]

17. Clique em OK na tela do editor do aplicativo lógico e clique em “Criar”. Serão necessários cerca de 30 segundos para a conclusão da criação.

18. Procure pelo aplicativo lógico recém-criado e clique em “Executar Agora” para iniciar uma execução.

19. Você pode verificar se uma nova conta com o nome “Conta da Microsoft” foi criada em sua conta do SugarCRM e se a mesma conta também foi atualizada com as informações do endereço de cobrança.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

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

<!---HONumber=August15_HO8-->