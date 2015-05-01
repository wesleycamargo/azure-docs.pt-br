<properties 
   pageTitle="Conector do SQL" 
   description="Como usar o conector do SQL" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Conector do Microsoft SQL #

Conectores podem ser usados em aplicativos de lógicos para obter, processar ou enviar dados como parte de um fluxo. Há situações em que você pode precisar trabalhar com o banco de dados SQL no SQL Azure ou no SQL Server (que é instalado no local e por trás do firewall). Utilizando o Conector do SQL em seu fluxo, você pode chegar a diversos resultados. Alguns exemplos:  

1.	Expor uma seção dos dados residentes no seu banco de dados SQL por meio de um front-end Web ou móvel do usuário.
2.	Inserir dados em sua tabela de banco de dados SQL para armazenamento (exemplo: registros de funcionários, pedidos de vendas etc.)
3.	Extrair dados do SQL para uso em um processo comercial

Nesses cenários, é necessário fazer o seguinte: 

1. Criar uma instância do Aplicativo de API do Conector do SQL
2. Estabelecer conectividade híbrida para o aplicativo de API se comunicar com o SQL local Essa etapa é opcional e é necessária apenas para o servidor SQL local e não para o SQL Azure.
3. Usar o aplicativo de API criado em um aplicativo lógico para obter o processo comercial desejado

	###Gatilhos e ações básicos
		
    - Dados de Votação (Gatilho) 
    - Inserir em Tabela
    - Atualizar Tabela
    - Selecionar de Tabela
    - Excluir de Tabela
    - Chamar Procedimento Armazenado

## Criar uma instância do Aplicativo de API do Conector do SQL ##

Para usar o Conector do SQL, você precisa criar uma instância do Aplicativo de API para o Conector do SQL. Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção "+NOVO" na parte inferior esquerda do Portal do Azure.
2. Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do SQL".
3. Forneça os detalhes genéricos, como nome, plano de serviço de aplicativo e assim por diante na primeira folha
4. Forneça as configurações mencionadas na tabela a seguir.	

<style type="text/css">
	table.tableizer-table {
	border: 1px solid #CCC; font-family: Arial, Helvetica, sans-serif;
	font-size: 12px;
} 
.tableizer-table td {
	padding: 4px;
	margin: 3px;
	border: 1px solid #ccc;
}
.tableizer-table th {
	background-color: #525B64; 
	color: #FFF;
	font-weight: bold;
}
</style><table class="tableizer-table">
<tr class="tableizer-firstrow"><th>Nome</th><th>Obrigatório</th><th>Valor Padrão</th><th>Descrição</th></tr>
 <tr><td>Nome do Servidor</td><td>Sim</td><td>&nbsp;</td><td>Especifique o nome do SQL Server. Exemplo: "SQLserver", "SQLserver/sqlexpress" ou "SQLserver.mydomain.com".</td></tr>
 <tr><td>Port</td><td>Não</td><td> 1433</td><td>Opcional. O número da porta em que a conexão é estabelecida. Se você não especificar um valor, o conector se conectará por meio da porta padrão.</td></tr>
 <tr><td>Nome de usuário</td><td>Sim</td><td>&nbsp;</td><td>Especifique um nome de usuário válido para se conectar ao SQL Server.</td></tr>
 <tr><td>Senha</td><td>Sim</td><td>&nbsp;</td><td>Especifique uma senha válida para se conectar ao SQL Server.</td></tr>
 <tr><td>Nome do Banco de Dados</td><td>Sim</td><td>&nbsp;</td><td>Especifique um nome de banco de dados válido no SQL Server. Exemplo: "orders" ou "dbo/orders" ou "myaccount/employees".</td></tr>
 <tr><td>Local</td><td>Sim</td><td>FALSE</td><td>Especifique se o SQL Server é local atrás de um firewall ou não. Se definido como TRUE, será necessário instalar um agente de escuta em um servidor que possa acessar o servidor SQL. Você pode ir para a página de resumo do aplicativo de API e clicar em 'Conexão Híbrida' para instalar o agente</td></tr>
 <tr><td>Cadeia de conexão do Barramento de Serviço</td><td>Não</td><td>&nbsp;</td><td>Opcional. Especifique esse parâmetro se o SQL Server for local. Deve ser uma cadeia de conexão válida do Namespace do Barramento de Serviço. Use a edição 'Padrão' do Barramento de Serviço do Azure e não a edição 'Básica'.</td></tr>
 <tr><td>Nome do Servidor Parceiro</td><td>Não</td><td>&nbsp;</td><td>Opcional. Especifique o servidor de parceiro ao qual se conectar quando servidor primário estiver inoperante.</td></tr>
 <tr><td>Tabelas</td><td>Não</td><td>&nbsp;</td><td>Opcional. Especifique as tabelas no banco de dados que podem ser modificadas pelo conector. Exemplo: OrdersTable, EmployeeTable</td></tr>
 <tr><td>Procedimentos Armazenados</td><td>Não</td><td>&nbsp;</td><td>Opcional. Especifique os procedimentos armazenados no banco de dados que podem ser chamados pelo conector. Exemplo: IsEmployeeEligible, CalculateOrderDiscount</td></tr>
 <tr><td>Consulta de Dados Disponíveis</td><td>Não</td><td>&nbsp;</td><td>Opcional. Especifique a instrução SQL para determinar se há dados disponíveis para sondar uma tabela de banco de dados do SQL Server. Exemplo: SELECT COUNT(*) from table_name.</td></tr>
 <tr><td>Sondar Consulta de Dados</td><td>Não</td><td>&nbsp;</td><td>Opcional. Especifique a instrução SQL para sondar a tabela de banco de dados do SQL Server. Você pode especificar qualquer número de instruções SQL separadas por ponto e vírgula. Exemplo: SELECT * from table_name; DELETE from table_name. OBSERVAÇÃO: Você precisa fornecer a instrução de sondagem de forma que ela não termine em um loop infinito. Por exemplo, select deve ser seguido por delete e select com base em um sinalizador e deve ser seguido pela atualização do sinalizador.</td></tr>
</table>


 ![][1]  

## Configuração Híbrida (opcional) ##

Observação: Essa etapa será necessária apenas se você estiver usando o SQL Server local por trás do firewall.

Navegue até o Aplicativo de API recém-criado, em Procurar -> Aplicativos de API -> < nome do aplicativo de API> e você verá o seguinte comportamento. A instalação está incompleta, pois a conexão híbrida ainda não foi estabelecida.

![][2] 

Para estabelecer a conectividade híbrida, faça o seguinte:

1. Copie a cadeia de conexão principal
2. Clique no link 'Baixar e Configurar'
3. Siga o processo de instalação que é iniciado e forneça a cadeia de conexão principal quando for solicitado
4. Quando o processo de instalação for concluído, uma caixa de diálogo semelhante a esta será exibida

![][3] 

Agora, quando navegar até o aplicativo de API, você observará que o status da conexão híbrida será Conectado. 

![][4] 

Observação: caso você queira trocar para a cadeia de conexão secundária, basta fazer novamente a configuração híbrida e fornecer a cadeia de conexão secundária em vez da cadeia principal  

## Uso em um aplicativo lógico ##

O Conector do SQL pode ser usado como um gatilho/ação em um aplicativo lógico. O gatilho e todas as ações dão suporte aos formatos de dados JSON e XML. Para cada tabela que é fornecida como parte das configurações de pacote, haverá um conjunto de ações de JSON e um conjunto de ações de XML. Se você estiver usando o gatilho/ação de XML, será possível usar o aplicativo de API de transformação para converter dados em outro formato de dados XML. 

Vamos examinar um aplicativo lógico simples que sonda os dados de uma tabela SQL, adiciona os dados a outra tabela e os atualiza.



-  Ao criar/editar um aplicativo lógico, escolha o Aplicativo de API do Conector do SQL criado como o gatilho. Isso listará os gatilhos disponíveis - Sondar Dados (JSON) e Sondar Dados (XML).

 ![][5] 


- Selecione o gatilho - Sondar Dados (JSON), especifique a frequência e clique em ✓.

![][6] 



- O gatilho aparecerá conforme configurado no aplicativo lógico. A(s) saída()s do gatilho será(ão) mostrada(s) e pode(m) ser usada(s) como entrada(s) em ações posteriores. 

![][7] 


- Selecione o mesmo conector do SQL da galeria como uma ação. Selecione uma das ações Inserir - inserir em TempEmployeeDetails (JSON).

![][8] 



- Forneça as entradas do registro a ser inserido e clique em ✓. 

![][9] 



- Selecione o mesmo conector do SQL da galeria como uma ação. Selecione a ação de atualização na mesma tabela (Ex.: Update EmployeeDetails)

![][11] 



- Forneça as entradas para a ação de atualização e clique em ✓. 

![][12] 

Você pode testar o aplicativo lógico adicionando um novo registro na tabela que está sendo sondada.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[2]: ./media/app-service-logic-connector-sql/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sql/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sql/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg




<!--HONumber=52-->