<properties 
   pageTitle="Conector do QuickBooks" 
   description="Como usar o Conector do QuickBooks" 
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


#Usando o Conector do QuickBooks em seu aplicativo lógico#

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. O Conector do QuickBooks permite criar e modificar entidades do QuickBooks diferentes. A seguir há uma lista de entidades do QuickBooks às quais o Conector do QuickBooks dá suporte.

<Table>
<TR><TD><B>Entidades</TD><TD><B>Descrição</TR>
<TR>	<TD>	Account	</TD>	<TD>	A Conta é um componente de um Plano de Contas e faz parte de um Razão. Usado para registrar um valor monetário total alocado em relação a um uso específico	</TD>	</TR>
<TR>	<TD>	Aviso de Crédito	</TD>	<TD>	O Aviso de Crédito é uma transação financeira que representa um reembolso ou crédito de pagamento ou parte de um pagamento por mercadorias ou serviços que foram vendidos.	</TD>	</TR>
<TR>	<TD>	Cliente	</TD>	<TD>	Um cliente é um consumidor do serviço ou produto que sua empresa oferece.	</TD>	</TR>
<TR>	<TD>	Estimativa	</TD>	<TD>	A Estimativa representa uma proposta para uma transação financeira de uma empresa a um cliente por mercadorias ou serviços propostos para serem vendidos, incluindo os preços propostos.	</TD>	</TR>
<TR>	<TD>	Fatura	</TD>	<TD>	Uma Fatura representa um formulário de venda em que o cliente paga por um produto ou serviço posteriormente. Informações adicionais sobre como usar o modelo de dados de Fatura podem ser encontradas aqui.	</TD>	</TR>
<TR>	<TD>	Item	</TD>	<TD>	Um item é algo que sua empresa compra, vende ou revende, como produtos, frete e manuseio de encargos, descontos e impostos sobre vendas (caso aplicável).  Um item é mostrado como uma linha em uma fatura ou outro formulário de venda.	</TD>	</TR>
<TR>	<TD>	SalesReceipt	</TD>	<TD>	Essa entidade representa o recibo de venda que é fornecido a um cliente.	</TD>	</TR>
</Table>


##Ações do QuickBooks ##
A seguir estão as diferentes ações disponíveis no Conector do QuickBooks.
	<table>
	<tbody>
		<tr><td>
		<strong>Ação</strong>
		</td>
		<td>
		<strong>Descrição</strong>
		</td>
		</tr>
		<tr>
		<td>
		Ler Entidade
		</td>
		<td>
		Ler um objeto de entidade.
		</td>
		</tr>
		<tr>
		<td>
		Criar ou Atualizar Entidade
		</td>
		<td>
		Criar ou atualizar um objeto de entidade. O objeto será atualizado se já existir; caso contrário, um novo objeto será criado.
		</td>
		</tr>
		<tr>
		<td>
		Excluir
		</td>
		<td>
		Essa ação exclui o objeto especificado da entidade selecionada.
		</td>
		</tr>
		<tr>	
		<td>
		Consultar
		</td>
		<td>
		A operação de consulta é o método para criar uma consulta guiada em relação a uma entidade.
		</td>
		</tr>
	</tbody>
	</table>

##Criar um aplicativo de API do Conector do QuickBooks##
1.	Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "QuickBooks".
3.	Configure o Conector do QuickBooks fornecendo os detalhes do Plano de Hospedagem e o grupo de recursos e selecionando o nome do Aplicativo de API.

	![][13]
4. Configure as Entidades do QuickBooks que você está interessado em ler/gravar nas 'Configurações do Pacote'.

Assim, agora você pode criar um Aplicativo de API do Conector do QuickBooks.


##Criar um aplicativo lógico##
Vamos criar um aplicativo lógico simples que cria uma conta no QuickBooks e atualiza o 'Tipo de Categoria' da mesma conta.

1.	Faça logon no Portal do Azure e clique em 'Novo -> Web + Móvel -> Aplicativo Lógico'

	![][1]

2.	Na página 'Criar aplicativo lógico', forneça os detalhes necessários, como nome, plano de serviço de aplicativo e local.

	![][2]

3.	Clique em 'Gatilhos e Ações', e a tela do editor de Aplicativos Lógicos aparecerá.

	![][3]

4.	Selecione 'Executar esta lógica manualmente', o que significa que o aplicativo lógico pode ser invocado apenas manualmente.


5.	Expanda 'Aplicativos de API neste grupo de recursos' na Galeria para ver todos os aplicativos de API disponíveis. Selecione 'Conector do QuickBooks' na galeria, e o 'Conector do QuickBooks' será adicionado ao fluxo.


6.	Você precisa autenticar e autorizar os aplicativos lógicos para executar operações em seu nome para o QuickBooks online. Para iniciar a autorização, clique em Autorizar no Conector do QuickBooks.

	![][4]

7.	Quando você clica em Autorizar, isso abre a caixa de diálogo de autenticação do QuickBooks. Forneça os detalhes de logon da conta do QuickBooks na qual você deseja executar as operações.

	![][5]

8. Conceda aos aplicativos lógicos acesso à sua conta para executar operações em seu nome clicando em Autorizar na caixa de diálogo de consentimento.

	![][6]

9.	Quando a autorização for concluída, todas as ações serão exibidas.

	![][7]

10.	Selecione 'Criar ou Atualizar Conta', e os parâmetros de entrada serão exibidos.

	![][8]

11.	Forneça o 'Nome' e o 'Tipo de Conta' e clique em ✓. 

	![][9]

12.	Selecione 'Conector do QuickBooks' na seção 'Usados Recentemente' na galeria, e uma nova ação do QuickBooks será adicionada.

13.	Selecione 'Criar ou Atualizar Conta' na lista de ações, e os parâmetros de entrada da ação serão exibidos.

	![][10]

14.	Clique em '+' ao lado de 'Id' para obter o valor da id na saída da ação Criar Conta. 

	![][11]

15.	Forneça novos valores para o Tipo de Conta e clique em ✓.

	![][12]

16. Clique em OK na tela do editor de aplicativo lógico e clique em 'Criar'. Serão necessários cerca de 30 segundos para a conclusão da criação.

17. Procure pelo aplicativo lógico recém-criado e clique em 'Executar' para iniciar uma execução.

18. Você pode verificar se uma nova conta com o nome 'Contoso' foi criada em sua conta do QuickBooks.

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



<!--HONumber=52-->