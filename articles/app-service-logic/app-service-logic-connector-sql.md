<properties 
   pageTitle="Usando o conector do SQL no Serviço de Aplicativo do Microsoft Azure" 
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
   ms.date="06/17/2015"
   ms.author="sutalasi"/>


# Conector do Microsoft SQL

Conecte-se ao SQL Server local ou a um banco de dados SQL do Azure para criar e alterar suas informações ou dados. Conectores podem ser usados em aplicativos lógicos para recuperar, processar ou enviar dados como parte de um “fluxo de trabalho”. Utilizando o Conector do SQL em seu fluxo de trabalho, você pode chegar a diversos resultados. Por exemplo, você pode:

- Expor uma seção dos dados residentes no seu banco de dados SQL usando um aplicativo da Web ou móvel. 
- Inserir dados em uma tabela de banco de dados SQL para armazenamento. Por exemplo, você pode inserir registros de funcionários, atualizar ordens de venda e assim por diante.
- Extrair dados do SQL para uso em um processo comercial. Por exemplo, você pode obter registros do cliente e colocá-los no SalesForce. 

## Gatilhos e ações
*Gatilhos* são eventos que ocorrem. Por exemplo, quando um pedido é atualizado ou quando um novo cliente é adicionado. Uma *ação* é o resultado do gatilho. Por exemplo, quando uma ordem é atualizada, enviar um alerta para o vendedor. Ou, quando um novo cliente for adicionado, enviar um email de boas-vindas para ele.

O conector do SQL pode ser usado como um gatilho ou uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. Para cada tabela incluída nas configurações do pacote (falaremos mais sobre isso posteriormente neste tópico), há um conjunto de ações de JSON e um conjunto de ações de XML.

O conector de SQL tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Sondar dados | <ul><li>Inserir na Tabela</li><li>Atualizar Tabela</li><li>Selecionar da Tabela</li><li>Excluir da Tabela</li><li>Chamar Procedimento Armazenado</li>

## Criar o conector do SQL

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Selecione **Aplicativos de API** e pesquise “Conector do SQL”.
3. Digite o nome, o plano do Serviço de Aplicativo e outras propriedades.
4. Insira as seguintes configurações de pacote:

	Nome | Obrigatório | Descrição
--- | --- | ---
Nome do Servidor | Sim | Especifique o nome do SQL Server. Por exemplo, digite *SQLserver/sqlexpress* ou *SQLserver.mydomain.com*.
Port | Não | O padrão é 1433.
Nome de usuário | Sim | Insira um nome de usuário que possa fazer logon no SQL Server. Caso esteja se conectando a um SQL Server local, digite o domínio/nome de usuário. 
Senha | Sim | Digite a senha do nome de usuário.
Nome do Banco de Dados | Sim | Informe o banco de dados ao qual está se conectando. Por exemplo, você pode inserir *Clientes* ou *dbo/pedidos*.
Local | Sim | O padrão é False. Insira False se estiver se conectando a um banco de dados SQL do Azure. Insira True caso esteja se conectando a um SQL Server local. 
Cadeia de conexão do Barramento de Serviço | Não | Se você estiver estabelecendo conexão local, insira a cadeia de conexão de retransmissão do Barramento de Serviço.<br/><br/>[Usando o Gerenciador de Conexão Híbrida](app-service-logic-hybrid-connection-manager.md)<br/>[Preços do Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/)
Nome do Servidor Parceiro | Não | Se o servidor primário não estiver disponível, você poderá informar um servidor parceiro como servidor alternativo ou de backup. 
Tabelas | Não | Lista as tabelas de banco de dados que podem ser atualizadas pelo conector. Por exemplo, digite *OrdersTable* ou *EmployeeTable*. Se nenhuma tabela for especificada, todas as tabelas poderão ser usadas. Tabelas válidas e/ou procedimentos armazenados são necessários para usar esse conector como uma ação. 
Procedimentos Armazenados | Não | Informe um procedimento armazenado existente que pode ser chamado pelo conector. Por exemplo, digite *sp_IsEmployeeEligible* ou *sp_CalculateOrderDiscount*. Tabelas válidas e/ou procedimentos armazenados são necessários para usar esse conector como uma ação. 
Consulta de Dados Disponíveis | Para suporte de gatilho | Instrução SQL para determinar se há dados disponíveis para sondar uma tabela de banco de dados do SQL Server. Deve retornar um valor numérico que representa o número de linhas de dados disponíveis. Exemplo: SELECT COUNT(*) from table_name. 
Sondar Consulta de Dados | Para suporte de gatilho | Instrução SQL para sondar a tabela de banco de dados do SQL Server. Você pode especificar qualquer número de instruções SQL separadas por ponto e vírgula. Essa instrução é executada transacionalmente e confirmada somente quando os dados são armazenados com segurança em seu aplicativo lógico. Exemplo: SELECT * FROM table_name; DELETE FROM table_name. <br/><br/>**Observação**<br/>Você deve fornecer uma instrução de pesquisa que evite um loop infinito. Para isso, exclua, mova ou atualize os dados selecionados para garantir que eles não sejam sondados novamente. 

5. Após a conclusão, as configurações de pacote são semelhantes às seguintes: <br/> ![][1]

## Usar o conector como um gatilho
Vamos examinar um aplicativo lógico simples que sonda os dados de uma tabela SQL, adiciona os dados a outra tabela e os atualiza.

Para usar o conector do SQL como um gatilho, informe os valores de **Consulta de Dados Disponíveis** e **Sondar Consulta de Dados**. **Consulta de Dados Disponíveis** é executada de acordo com o agendamento informado e determina se há dados disponíveis. Como essa consulta retorna apenas um número escalar, ela pode ser ajustada e otimizada para execução frequente.

**Sondar Consulta de Dados** só é executado quando a consulta de dados disponíveis indica que há dados disponíveis. Essa instrução é executada em uma transação e só é confirmada quando os dados extraídos são armazenados permanentemente no fluxo de trabalho. É importante evitar extrair novamente os mesmos dados por tempo indefinido. A natureza transacional dessa execução pode ser usada para excluir ou atualizar os dados a fim de garantir que eles não sejam coletados na próxima consulta.

> [AZURE.NOTE]O esquema retornado por essa instrução identifica as propriedades disponíveis em seu conector. Todas as colunas devem ser nomeadas.

#### Exemplo de Consulta de Dados Disponíveis

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### Exemplo de Sondar Consulta de Dados

	SELECT *, GetData() as 'PollTime' FROM [Order] 
		WHERE OrderStatus = 'ProcessedForCollection' 
		ORDER BY Id DESC; 
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk' 
		WHERE Id = 
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### Adicionar o gatilho
1. Ao criar ou editar um aplicativo lógico, selecione o conector do SQL criado como gatilho. Isso listará os gatilhos disponíveis: **Sondar Dados (JSON)** e **Sondar Dados (XML)**: <br/> ![][5] 

2. Selecione o gatilho **Sondar Dados (JSON)**, especifique a frequência e clique em ✓: <br/> ![][6]

3. O gatilho aparecerá conforme configurado no aplicativo lógico. As saídas do gatilho serão mostradas e poderão ser usadas como entradas em ações posteriores: <br/> ![][7]

## Usar o conector como uma ação
Usaremos o cenário de aplicativo lógico simples que sonda os dados de uma tabela SQL, adiciona os dados a outra tabela e os atualiza.

Para usar o conector do SQL como uma ação, insira o nome de tabelas e/ou procedimentos armazenados que você inseriu quando você criou o conector do SQL:

1. Após o gatilho (ou escolha 'executar esta lógica manualmente'), adicione o conector do SQL criado na galeria. Selecione uma das ações Inserir, como *Inserir em TempEmployeeDetails (JSON)*: <br/> ![][8] 

2. Especifique as entradas do registro a ser inserido e clique em ✓: <br/> ![][9]

3. Na galeria, selecione o mesmo conector do SQL que você criou. Como uma ação, selecione a ação de atualização na mesma tabela, como *Atualizar EmployeeDetails*: <br/> ![][11]

4. Insira os valores de entrada para a ação de atualização e clique em ✓: <br/> ![][12]

Você pode testar o aplicativo lógico adicionando um novo registro na tabela que está sendo sondada.

## Configuração Híbrida (opcional)

> [AZURE.NOTE]Essa etapa será necessária apenas se você estiver usando o SQL Server local por trás do firewall.

O Serviço de Aplicativo usa o Gerenciador de Configuração Híbrida para se conectar com segurança ao sistema local. Se seu conector usar um SQL Server local, o Gerenciador de Conexão Híbrida será necessário.

Consulte [Usando o Gerenciador de Conexão Híbrida](app-service-logic-hybrid-connection-manager.md).


## Faça mais com seu conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um aplicativo lógico. Consulte [O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md).

Você também pode analisar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conector](../app-service-api/app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png


 

<!---HONumber=62-->