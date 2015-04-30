<properties 
   pageTitle="Conector do Oracle" 
   description="Como usar o conector do Oracle" 
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


# Conector do Banco de Dados Oracle #

Conectores podem ser usados em aplicativos de lógicos para obter, processar ou enviar dados como parte de um fluxo. Utilizando o Conector do Oracle em seu fluxo, você pode chegar a diversos resultados. Alguns exemplos:  

1.	Expor uma seção dos dados residentes no seu banco de dados Oracle por meio de um front-end Web ou móvel do usuário.
2.	Inserir dados em sua tabela de banco de dados Oracle para armazenamento (exemplo: registros de funcionários, pedidos de vendas etc.)
3.	Extrair dados do Oracle para uso em um processo comercial

Nesses cenários, é necessário fazer o seguinte: 

1. Criar uma instância do Aplicativo de API do Conector do Oracle
2. Estabelecer conectividade híbrida para o aplicativo de API se comunicar com o servidor Oracle local
3. Usar o aplicativo de API criado em um aplicativo lógico para obter o processo comercial desejado

	###Gatilhos e ações básicos
		
    - Dados de Sondagem (Gatilho) 
    - Inserir em Tabela
    - Atualizar Tabela
    - Selecionar de Tabela
    - Excluir de Tabela
    - Chamar Procedimento Armazenado

## Criar uma instância do Aplicativo de API do Conector do Banco de Dados Oracle ##

Para usar o Conector do Oracle, você precisa criar uma instância do Aplicativo de API para o Conector do Oracle. Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção '+ NEW' na parte inferior esquerda do Portal do Azure.
2. Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do Oracle".
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
<tr class="tableizer-firstrow"><th>Nome</th><th>Obrigatório</th><th>Descrição</th></tr>
 <tr><td>Fonte de dados</td><td>Sim</td><td>Um nome de fonte de dados (serviço de rede) que é especificado no arquivo tnsnames.ora no computador em que o cliente Oracle está instalado. Para obter mais informações sobre nomes de fonte de dados e tnsnames.ora, consulte [Configurando o cliente Oracle]</td></tr>
 <tr><td>Nome de usuário</td><td>Sim</td><td>Especifique um nome de usuário válido para se conectar ao servidor Oracle.</td></tr>
 <tr><td>Senha</td><td>Sim</td><td>Especifique uma senha válida para se conectar ao servidor Oracle.</td></tr>
 <tr><td>Cadeia de conexão do Barramento de Serviço</td><td>Sim</td><td>Opcional. Especifique esse parâmetro se o servidor Oracle for local. Deve ser uma cadeia de conexão válida do Namespace do Barramento de Serviço. Você precisa instalar um agente de escuta em um servidor que possa acessar seu servidor Oracle. Você pode ir para a página de resumo do aplicativo de API e clicar em Conexão Híbrida' para instalar o agente.</td></tr>
 <tr><td>Tabelas</td><td>Não</td><td>Opcional. Especifique as tabelas no banco de dados que podem ser modificadas pelo conector. Exemplo: OrdersTable, EmployeeTable</td></tr>
 <tr><td>Procedimentos Armazenados</td><td>Não</td><td>Opcional. Especifique os procedimentos armazenados no banco de dados que podem ser chamados pelo conector. Ex: IsEmployeeEligible, CalculateOrderDiscount</td></tr>
 <tr><td>Funções</td><td>Não</td><td>Opcional. Especifique as funções no banco de dados que podem ser chamadas pelo conector. Ex: IsEmployeeEligible, CalculateOrderDiscount</td></tr>
 <tr><td>Entidades de Pacote</td><td>Não</td><td>Opcional. Especifique os pacotes no banco de dados que podem ser chamados pelo conector. Ex.: PackageOrderProcessing.CompleteOrder, PackageOrderProcessing.GenerateBill</td></tr>
 <tr><td>Instrução de Dados Disponíveis</td><td>Não</td><td>Opcional. Especifique a instrução para determinar se há dados disponíveis para sondagem. Exemplo: SELECT * from table_name</td></tr>
 <tr><td>Tipo de Sondagem</td><td>Não</td><td>Opcional. Especifique o tipo de sondagem. Os valores permitidos são "Select", "Procedure", "Function" e "Package"</td></tr>
 <tr><td>Instrução de Sondagem</td><td>Não</td><td>Opcional. Especifique a instrução para sondar o banco de dados do servidor Oracle. Exemplo: SELECT * from table_name</td></tr>
 <tr><td>Instrução de Sondagem de Postagem</td><td>Não</td><td>Opcional. Especifique a instrução a ser executada após a sondagem. Exemplo: DELETE * from table_name.</td></tr>
</table>



 ![][1]  

## Configuração Híbrida ##

Navegue até o Aplicativo de API recém-criado, em Procurar -> Aplicativos de API -> <nome do aplicativo de API> e você verá o seguinte comportamento. A instalação está incompleta, pois a conexão híbrida ainda não foi estabelecida.

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

O Conector do Oracle pode ser usado como um gatilho/ação em um aplicativo lógico. O gatilho e todas as ações dão suporte aos formatos de dados JSON e XML. Para cada tabela que é fornecida como parte das configurações de pacote, haverá um conjunto de ações de JSON e um conjunto de ações de XML. Se você estiver usando o gatilho/ação de XML, será possível usar o aplicativo de API de transformação para converter dados em outro formato de dados XML. 

Vamos examinar um aplicativo lógico simples que sonda os dados de uma tabela Oracle, adiciona os dados a outra tabela e os atualiza.



-  Ao criar/editar um aplicativo lógico, escolha o Aplicativo de API do Conector do Oracle criado como o gatilho. Isso listará os gatilhos disponíveis - Sondar Dados (JSON) e Sondar Dados (XML).

 ![][5] 


- Selecione o gatilho - Sondar Dados (JSON), especifique a frequência e clique em ✓.

![][6] 



- O gatilho aparecerá conforme configurado no aplicativo lógico. A(s) saída()s do gatilho será(ão) mostrada(s) e pode(m) ser usada(s) como entrada(s) em ações posteriores. 

![][7] 


- Selecione o mesmo conector do Oracle da galeria como uma ação. Selecione uma das ações Inserir - inserir em TempEmployeeDetails (JSON).

![][8] 



- Forneça as entradas do registro a ser inserido e clique em ✓. 

![][9] 



- Selecione o mesmo conector do Oracle da galeria como uma ação. Selecione a ação de atualização na mesma tabela (Ex.: Update EmployeeDetails)

![][11] 



- Forneça as entradas para a ação de atualização e clique em ✓. 

![][12] 

Você pode testar o aplicativo lógico adicionando um novo registro na tabela que está sendo sondada.

<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.jpg
[2]: ./media/app-service-logic-connector-oracle/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-oracle/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-oracle/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.jpg

<!--Links-->
[Configurando o cliente Oracle]: https://msdn.microsoft.com/pt-br/library/dd787872.aspx


<!--HONumber=52-->