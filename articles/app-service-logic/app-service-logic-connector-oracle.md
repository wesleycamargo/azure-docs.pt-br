<properties
   pageTitle="Usando o Conector do Oracle no Serviço de Aplicativo do Microsoft Azure"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Conector do Banco de Dados Oracle

Conecte-se a um servidor local de Banco de Dados Oracle para criar e alterar suas informações ou dados. Conectores podem ser usados em Aplicativos de Lógicos para obter, processar ou enviar dados como parte de um “fluxo de trabalho”. Quando usa o Conector do Oracle em seu fluxo de trabalho, você pode chegar a diversos resultados. Por exemplo, você pode:

- Expor uma seção dos dados residentes no seu banco de dados Oracle usando um aplicativo Web ou móvel.
- Inserir dados na tabela do banco de dados Oracle para armazenamento. Por exemplo, você pode inserir registros de funcionários, atualizar ordens de venda e assim por diante.
- Obter dados do Oracle para uso em um processo comercial. Por exemplo, você pode obter registros do cliente e colocá-los no SalesForce.


## Gatilhos e Ações
*Gatilhos* são eventos que ocorrem. Por exemplo, quando um pedido é atualizado ou quando um novo cliente é adicionado. Uma *Ação* é o resultado do gatilho. Por exemplo, quando uma ordem é atualizada, enviar um alerta para o vendedor. Ou, quando um novo cliente for adicionado, enviar um email de boas-vindas para ele.

O conector do Banco de Dados Oracle pode ser usado como um gatilho ou uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. Para cada tabela incluída nas configurações do pacote (falaremos mais sobre isso posteriormente neste tópico), há um conjunto de ações de JSON e um conjunto de ações de XML. Se você estiver usando um gatilho/ação de XML, será possível usar o [Aplicativo de API de Transformação](app-service-logic-transform-xml-documents.md) para converter dados em outro formato de dados XML.

O Conector de Banco de Dados Oracle tem os seguintes Gatilhos e Ações disponíveis:

Gatilhos | Ações
--- | ---
Sondar dados | <ul><li>Inserir na Tabela</li><li>Atualizar Tabela</li><li>Selecionar da Tabela</li><li>Excluir da Tabela</li><li>Chamar Procedimento Armazenado</li>


## Criar um Conector do Banco de Dados Oracle

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Selecione **Aplicativos de API** e pesquise “Conector de Banco de Dados Oracle”.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades.
4. Insira as seguintes configurações de pacote:

	Nome | Obrigatório | Descrição
--- | --- | ---
Fonte de dados | Sim | Um nome de fonte de dados (serviço de rede) que é especificado no arquivo tnsnames.ora no computador em que o cliente Oracle está instalado. Para obter informações sobre nomes de fonte de dados e tnsnames.ora, consulte [Configurando o Cliente Oracle](http://msdn.microsoft.com/library/dd787872.aspx).
Nome de usuário | Sim | Insira um nome de usuário para se conectar ao servidor Oracle.
Senha | Sim | Digite a senha do nome de usuário.
Cadeia de conexão do Barramento de Serviço | Sim | Se você estiver estabelecendo conexão local, insira a cadeia de conexão de retransmissão do Barramento de Serviço.<br/><br/>[Usando o Gerenciador de Conexão Híbrida](app-service-logic-hybrid-connection-manager.md)<br/>[Preços do Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/)
Tabelas | Não | Insira as tabelas no banco de dados que podem ser modificadas pelo conector. Por exemplo, digite *OrdersTable, EmployeeTable*.
Procedimentos Armazenados | Não | Insira os procedimentos armazenados no banco de dados que podem ser chamados pelo conector. Por exemplo, insira *IsEmployeeEligible, CalculateOrderDiscount*.
Funções | Não | Insira as funções no banco de dados que podem ser chamadas pelo conector. Por exemplo, insira *IsEmployeeEligible, CalculateOrderDiscount*.
Entidades de Pacote | Não | Insira os pacotes no banco de dados que podem ser chamados pelo conector. Por exemplo, insira *PackageOrderProcessing.CompleteOrder, PackageOrderProcessing.GenerateBill*.
Instrução de Dados Disponíveis | Não | Insira a instrução para determinar se há dados disponíveis para sondagem. Por exemplo, digite *SELECT * from nome_da_tabela*.
Tipo de Sondagem | Não | Especifique o tipo de sondagem. Os valores permitidos são "Select", "Procedure", "Function" e "Package".
Instrução de Sondagem | Não | Insira a instrução para sondar o banco de dados do servidor Oracle. Por exemplo, digite *SELECT * from nome_da_tabela*.
Instrução de Sondagem de Postagem | Não | Insira a instrução a ser executada após a sondagem. Por exemplo, insira *DELETE * from nome_da_tabela*.

5. Após a conclusão, as configurações de pacote são semelhantes às seguintes: <br/> ![][1]


## Usar o Conector como um Gatilho
Vamos examinar um aplicativo lógico simples que sonda os dados de uma tabela Oracle, adiciona os dados a outra tabela e os atualiza.

### Adicionar o Gatilho
1. Ao criar ou editar um aplicativo lógico, selecione o Conector do Oracle criado como o gatilho. Isso lista os gatilhos disponíveis: **Sondar Dados (JSON)** e **Sondar Dados (XML)**: <br/> ![][5]

2. Selecione o gatilho **Sondar Dados (JSON)**, insira a frequência e clique em ✓: <br/> ![][6]

3. O gatilho agora aparece conforme configurado no aplicativo lógico. A saída(s) do gatilho será mostrada e pode ser usada como entradas em quaisquer ações posteriores: <br/> ![][7]

## Usar o Conector como uma Ação
Uso de nosso aplicativo lógico simples, que sonda os dados de uma tabela Oracle, adiciona os dados a outra tabela e os atualiza.

Para usar o Conector do Oracle como uma ação, insira o nome de tabelas e/ou procedimentos armazenados que você inseriu quando criou o Conector do Oracle:

1. Selecione o mesmo conector do Oracle da galeria como uma ação. Selecione uma das ações Inserir, como *Inserir em TempEmployeeDetails (JSON)*: <br/> ![][8]

2. Especifique os valores de entrada do registro a ser inserido e clique em ✓: <br/> ![][9]

3. Na galeria, selecione o mesmo Conector do Oracle que você criou. Como uma ação, selecione a ação Atualizar na mesma tabela, como *Atualizar TempEmployeeDetails*: <br/> ![][11]

4. Insira os valores de entrada para a ação de atualização e clique em ✓: <br/> ![][12]

Você pode testar o aplicativo lógico adicionando um novo registro na tabela que está sendo sondada.

## Configuração Híbrida

> [AZURE.NOTE]Essa etapa será necessária apenas se você estiver usando o Oracle local por trás do firewall.

O Serviço de Aplicativo usa o Gerenciador de Configuração Híbrida para se conectar com segurança ao sistema local. Se seu conector usar um Oracle local, o Gerenciador de Conexão Híbrida será necessário.

Consulte [Usando o Gerenciador de Conexão Híbrida](app-service-logic-hybrid-connection-manager.md).

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e Monitorar aplicativos de API e conector](app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png

<!---HONumber=July15_HO3-->