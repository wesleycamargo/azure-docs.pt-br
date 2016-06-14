<properties
   pageTitle="Usando o conector do QuickBooks em Aplicativos lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o conector do QuickBooks ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="sameerch"/>


# Introdução ao conector QuickBooks e adição dele ao seu Aplicativo lógico
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview dos Aplicativos Lógicos.

Use o conector do QuickBooks para criar e modificar entidades diferentes do QuickBooks. A tabela a seguir lista as entidades com suporte:

Entidades|Descrição
---|---
Conta|A Conta é um componente de um Plano de Contas e faz parte de um Razão. Usado para registrar um valor monetário total alocado em relação a um uso específico
Aviso de Crédito|O Aviso de Crédito é uma transação financeira que representa um reembolso ou crédito de pagamento ou parte de um pagamento por mercadorias ou serviços que foram vendidos.
Cliente|Um cliente é um consumidor do serviço ou produto que sua empresa oferece.
Estimativa|A Estimativa representa uma proposta para uma transação financeira de uma empresa a um cliente por mercadorias ou serviços propostos para serem vendidos, incluindo os preços propostos.
Fatura|Uma Fatura representa um formulário de venda em que o cliente paga por um produto ou serviço posteriormente. Informações adicionais sobre como usar o modelo de dados de Fatura podem ser encontradas aqui.
Item|Um item é algo que sua empresa compra, vende ou revende, como produtos, frete e manuseio de encargos, descontos e impostos sobre vendas (caso aplicável). Um item é mostrado como uma linha em uma fatura ou outro formulário de venda.
SalesReceipt|Essa entidade representa o recibo de venda que é fornecido a um cliente.

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o conector do QuickBooks a seu fluxo de trabalho de negócios e processar os dados como parte desse fluxo de trabalho dentro de um Aplicativo lógico.

##Ações do QuickBooks ##
A seguir estão as diferentes ações disponíveis no Conector do QuickBooks.

Ação|Descrição
---|---
Ler Entidade|Ler um objeto de entidade.
Criar ou Atualizar Entidade|Criar ou atualizar um objeto de entidade. O objeto será atualizado se já existir; caso contrário, um novo objeto será criado.
Excluir|Essa ação exclui o objeto especificado da entidade selecionada.
Consultar|A operação de consulta é o método para criar uma consulta guiada em relação a uma entidade.

##Criar um aplicativo de API do conector do QuickBooks##
1.	Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "QuickBooks".
3.	Configure o conector do QuickBooks fornecendo os detalhes do Plano de Hospedagem, o grupo de recursos e selecionando o nome do aplicativo de API.

	![][13]
4. Configure as Entidades do QuickBooks que você está interessado em ler/gravar nas “Configurações do Pacote”.

Com isso, agora você poderá criar um aplicativo de API do Conector do QuickBooks.


##Criar um Aplicativo lógico##
Iremos criar um Aplicativo lógico simples que cria uma conta no QuickBooks e atualiza o “Tipo de Categoria” da mesma conta.

1.	Fazer logon no Portal do Azure e clicar em ‘Novo -> Web + celular -> Aplicativo lógico’

	![][1]

2.	Na página “Criar aplicativo lógico”, forneça os detalhes necessários, como nome, plano de serviço de aplicativo e local.

	![][2]

3.	Clique em “Gatilhos e Ações” e a tela do editor do Aplicativo lógico aparecerá.

	![][3]

4.	Selecione ‘Executar esta lógica manualmente’, o que significa que o Aplicativo lógico só poderá ser chamado manualmente.


5.	Expanda ‘Aplicativos de API neste grupo de recursos’ na Galeria para ver todos os aplicativos de API disponíveis. Selecione ‘Conector do QuickBooks’ na galeria e ele será adicionado ao fluxo.


6.	Você precisaria autenticar e autorizar os Aplicativos lógicos para executar as operações em seu nome, caso o QuickBooks esteja online. Para iniciar a autorização, clique em Autorizar no conector do QuickBooks.

	![][4]

7.	Quando você clica em Autorizar, isso abre a caixa de diálogo de autenticação do QuickBooks. Forneça os detalhes de logon da conta do QuickBooks na qual você deseja executar as operações.

	![][5]

8. Conceda aos Aplicativos lógicos acesso à sua conta para executar a operação em seu nome clicando em Autorizar na caixa de diálogo de consentimento.

	![][6]

9.	Quando a autorização for concluída, todas as ações serão exibidas.

	![][7]

10.	Selecione a ação “Criar ou Atualizar Conta”, e os parâmetros de entrada serão exibidos.

	![][8]

11.	Forneça o “Nome” e o “Tipo de Conta” e clique em ✓.

	![][9]

12.	Selecione ‘Conector do QuickBooks’ na seção ‘Usados Recentemente’ na galeria e uma nova ação do QuickBooks será adicionada.

13.	Selecione “Criar ou Atualizar Conta” na lista de ações, e os parâmetros de entrada da ação serão exibidos.

	![][10]

14.	Clique em “+” ao lado do “Id” para obter o valor da id na saída da ação Criar Conta.

	![][11]

15.	Forneça novos valores para o Tipo de Conta e clique em ✓.

	![][12]

16. Clique em OK na tela do editor do aplicativo lógico e clique em “Criar”. Serão necessários cerca de 30 segundos para a conclusão da criação.

17. Procure o Aplicativo lógico recém-criado e clique em ‘Executar’ para iniciar uma execução.

18. Você pode verificar se uma nova conta com o nome “Contoso” foi criada na sua conta do QuickBooks.

## Fazer mais com seu conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você quiser começar com os Aplicativos lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Aplicativo Lógico](https://tryappservice.azure.com/?appservice=logic), onde poderá criar imediatamente um Aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Confira [Gerenciar e Monitorar seus Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png

<!---HONumber=AcomDC_0601_2016-->