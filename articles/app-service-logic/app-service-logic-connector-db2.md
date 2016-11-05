---
title: Usando o conector DB2 no Serviço de Aplicativo do Microsoft Azure | Microsoft Docs
description: Como usar o conector DB2 com gatilhos e ações do Aplicativo lógico
services: logic-apps
documentationcenter: .net,nodejs,java
author: gplarsen
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: plarsen

---
# Conector DB2
> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview dos Aplicativos Lógicos.
> 
> 

O conector da Microsoft para o DB2 é um aplicativo de API para conectar os aplicativos, por meio do Serviço de Aplicativo do Azure, aos recursos armazenados em um banco de dados IBM DB2. O conector inclui um Cliente da Microsoft para conectar os servidores DB2 remotos em uma conexão de rede TCP/IP, incluindo as conexões híbridas do Azure com os servidores DB2 locais usando o conector de Retransmissão do Barramento de Serviço do Azure que suporta as seguintes operações do banco de dados:

* Leitura de linhas usando SELECT
* Sondagem para leitura de linhas usando SELECT COUNT seguido por SELECT
* Adição de uma linha ou várias (em massa) linhas usando INSERT
* Adição de uma linha ou várias (em massa) linhas usando UPDATE
* Remoção de uma linha ou várias (em massa) linhas usando DELETE
* Leitura para alteração de linhas usando SELECT CURSOR seguido por UPDATE WHERE CURRENT OF CURSOR
* Leitura para remoção de linhas usando SELECT CURSOR seguido por UPDATE WHERE CURRENT OF CURSOR
* Execução de procedimento com parâmetros de entrada e saída, valor de retorno, conjunto de resultados, usando CALL
* Comandos personalizados e operações compostas usando SELECT, INSERT, UPDATE e DELETE

## Gatilhos e Ações
O conector oferece suporte aos seguintes gatilhos e ações de aplicativo lógico:

| Gatilhos | Ações |
| --- | --- |
| <ul><li>Sondar dados</li></ul> |<ul><li>Inserção em massa</li><li>Inserção</li><li>Atualização em massa</li><li>Atualização</li><li>Chamada</li><li>Exclusão em massa</li><li>Exclusão</li><li>Seleção</li><li>Atualização condicional</li><li>Postagem em EntitySet</li><li>Exclusão condicional</li><li>Seleção de entidade única</li><li>Exclusão</li><li>Upsert para EntitySet</li><li>Comandos personalizados</li><li>Operações compostas</li></ul> |

## Criar o conector DB2
Você pode definir um conector em um Aplicativo lógico ou no Azure Marketplace, como mostra o exemplo a seguir:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Na folha **Tudo**, digite **db2** na caixa **Pesquisar Tudo** e tecle Enter.
3. No painel de resultados Pesquisar Tudo, escolha **Conector DB2**.
4. Na folha de descrição do conector DB2, escolha **Criar**.
5. Na folha do pacote do conector DB2, digite o Nome (por exemplo, "Db2ConnectorNewOrders"), o Plano de Serviço de Aplicativo e outras propriedades.
6. Escolha **Configurações do pacote** e defina as seguintes configurações do pacote:
   
   | Nome | Obrigatório | Descrição |
   | --- | --- | --- |
   | ConnectionString |Sim |Cadeia de conexão do cliente DB2 (por exemplo, "Network Address=servername;Network Port=50000;User ID=username;Password=password;Initial Catalog=SAMPLE;Package Collection=NWIND;Default Schema=NWIND"). |
   | Tabelas |Sim |Lista separada por vírgulas de nomes de tabela, exibições e alias necessários para operações de OData e para gerar a documentação do swagger com exemplos (por exemplo, "*NEWORDERS*"). |
   | Procedimentos |Sim |Lista separada por vírgulas de nomes de procedimentos e de funções (por exemplo, "SPORDERID"). |
   | OnPremise |Não |Implantação no local usando a Retransmissão do Barramento de Serviço do Azure. |
   | ServiceBusConnectionString |Não |A cadeia de conexão da Retransmissão do Barramento de Serviço do Azure. |
   | PollToCheckData |Não |Instrução SELECT COUNT a ser usada com um gatilho de aplicativo lógico (por exemplo, "SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL"). |
   | PollToReadData |Não |Instrução SELECT a ser usada com um gatilho de aplicativo lógico (por exemplo, "SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE"). |
   | PollToAlterData |Não |Instrução UPDATE ou DELETE a ser usada com um gatilho de aplicativo lógico (por exemplo, "UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;"). |
7. Escolha **OK** e, em seguida, **Criar**.
8. Após a conclusão, as Configurações do Pacote serão semelhantes às seguintes: ![][1]

## Aplicativo lógico com ação do conector DB2 para adicionar dados
Você pode definir uma ação de aplicativo lógico para adicionar dados a uma tabela do DB2 usando uma operação OData de Inserção de API ou Postagem para Entidade. Por exemplo, você pode inserir um novo registro de pedido de cliente processando uma instrução SQL INSERT em uma tabela definida com uma coluna de identidade, retornando para o aplicativo lógico o valor de identidade ou as linhas afetadas (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [!TIP]
> A Conexão do DB2 "*Postagem em EntitySet*" retorna o valor da coluna de identidade e "*Inserção de API*" retorna as linhas afetadas
> 
> 

1. No quadro inicial do Azure, escolha **+** (sinal de adição), **Web + Móvel**, depois, **Aplicativo lógico**.
2. Digite o Nome (por exemplo, "NewOrdersDb2"), o Plano de Serviço de Aplicativo, outras propriedades e depois escolha **Criar**.
3. No quadro inicial do Azure, escolha o aplicativo lógico que você acabou de criar, **Configurações** e **Gatilhos e ações**.
4. Na folha Gatilhos e ações, escolha **Criar do Zero** em Modelos do Aplicativo lógico.
5. No painel Aplicativos de API, escolha **Recorrência**, defina a frequência e o intervalo e clique na **marca de seleção**.
6. No painel Aplicativos de API, escolha **Conector DB2** e expanda a lista de operações para selecionar **Inserir em NEWORDER**.
7. Expanda a lista de parâmetros para inserir os valores a seguir:
   
   | Nome | Valor |
   | --- | --- |
   | CUSTID |10042 |
   | SHIPNAME |Lazy K Kountry Store |
   | SHIPADDR |12 Orchestra Terrace |
   | SHIPCITY |Walla Walla |
   | SHIPREG |WA |
   | SHIPZIP |99362 |
8. Escolha a **marca de seleção** para salvar as configurações da ação e depois escolha **Salvar**.
9. As configurações devem ter a seguinte aparência: ![][3]
10. Na lista **Todas as execuções** em **Operações**, selecione o primeiro item da lista (execução mais recente).
11. Na folha **Execução do aplicativo lógico**, escolha o item de **AÇÃO** **db2connectorneworders**.
12. Na folha **Ação do aplicativo lógico**, escolha **LINK DE ENTRADAS**. O conector DB2 usa as entradas para processar uma instrução INSERT com parâmetros.
13. Na folha **Ação do aplicativo lógico**, escolha **LINK DE SAÍDAS**. As entradas devem ter a seguinte aparência: ![][4]

#### O que você precisa saber
* O Conector trunca os nomes de tabela do DB2 ao formar nomes de ação do aplicativo lógico. Por exemplo, a operação **Inserir em NEWORDERS** será truncada para **Inserir em NEWORDER**.
* Depois de salvar os **Gatilhos e ações** do aplicativo lógico, a lógica do aplicativo processará a operação. Pode haver um atraso de alguns segundos (por exemplo, 3 a 5 segundos) antes de o aplicativo lógico processar a operação. Opcionalmente, você pode clicar em **Executar Agora** para processar a operação.
* O conector DB2 define os membros de EntitySet com atributos, inclusive se o membro corresponde a uma coluna do DB2 com uma coluna padrão ou colunas geradas (por exemplo, identidade). O aplicativo lógico exibe um asterisco vermelho ao lado do nome do ID de membro de EntitySet, para indicar as colunas do DB2 que exigem valores. Você não deve inserir um valor para o membro ORDID, que corresponde à coluna de identidade do DB2. Você pode inserir valores para outros membros opcionais (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), que correspondem às colunas do DB2 com valores padrão.
* O conector DB2 retorna ao Aplicativo lógico a resposta na Postagem para EntitySet, que inclui os valores para as colunas de identidade, derivadas do SQLDARD (Dados de Resposta da Área de Dados do SQL) da DRDA na instrução SQL INSERT preparada. O servidor do DB2 não retorna os valores inseridos para colunas com valores padrão.

## Aplicativo lógico com ação do conector DB2 para adicionar dados em massa
Você pode definir uma ação de aplicativo lógico para adicionar dados a uma tabela do DB2 usando uma operação de Inserção em Massa de API. Por exemplo, você pode inserir dois registros novos de pedido de cliente processando uma instrução SQL INSERT usando uma matriz de valores de linha em uma tabela definida com uma coluna de identidade, retornando as linhas afetadas para o aplicativo lógico (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. No quadro inicial do Azure, escolha **+** (sinal de adição), **Web + Móvel**, depois, **Aplicativo lógico**.
2. Digite o Nome (por exemplo, "NewOrdersBulkDb2"), o Plano de Serviço de Aplicativo, outras propriedades e selecione **Criar**.
3. No quadro inicial do Azure, escolha o aplicativo lógico que você acabou de criar, **Configurações** e **Gatilhos e ações**.
4. Na folha Gatilhos e ações, escolha **Criar do Zero** em Modelos do Aplicativo lógico.
5. No painel Aplicativos de API, escolha **Recorrência**, defina a frequência e o intervalo e clique na **marca de seleção**.
6. No painel Aplicativos de API, escolha **Conector DB2** e expanda a lista de operações para selecionar **Inserir em Massa em NEW**.
7. Insira o valor de **linhas** como uma matriz. Por exemplo, copie e cole o seguinte:
   
    ```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
    ```
8. Escolha a **marca de seleção** para salvar as configurações da ação e depois escolha **Salvar**. As configurações devem ter a seguinte aparência: ![][6]
9. Na lista **Todas as execuções**, em **Operações**, clique no primeiro item da lista (execução mais recente).
10. Na folha **Execução do aplicativo lógico**, clique no item **AÇÃO**.
11. Na folha **Ação do aplicativo lógico**, clique no **LINK DE ENTRADAS**. As saídas devem ter a seguinte aparência: [][7]
12. Na folha **Ação do aplicativo lógico**, clique no **LINK DE SAÍDAS**. As saídas devem ter a seguinte aparência: ![][8]

#### O que você precisa saber
* O Conector trunca os nomes de tabela do DB2 ao formar nomes de ação do aplicativo lógico. Por exemplo, a operação **Inserir em Massa em NEWORDERS** será truncada para **Inserir em Massa em NEW**.
* O banco de dados do DB2 gera valores ao omitir as colunas de identidade (por exemplo, ORDID), as colunas anuláveis (por exemplo, SHIPDATE) e as colunas com valores padrão (por exemplo, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY).
* Ao especificar "hoje" e "amanhã", o conector DB2 gera as funções "CURRENT DATE" e "CURRENT DATE + 1 DAY" (por exemplo, REQDATE).

## Aplicativo lógico com o gatilho do conector DB2 para ler, alterar ou excluir dados
Você pode definir um gatilho de aplicativo lógico para pesquisar e ler os dados de uma tabela do DB2 usando uma operação composta Sondar Dados de API. Por exemplo, você pode ler um ou mais registros novos de pedido do cliente, retornando os registros para o Aplicativo lógico. As configurações de pacote/aplicativo da Conexão do DB2 são parecidas com as seguintes:

    App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <nenhum valor especificado>

Além disso, você pode definir um gatilho de aplicativo lógico para pesquisar, ler e alterar os dados de uma tabela do DB2 usando uma operação composta Sondar Dados de API. Por exemplo, você pode ler um ou mais registros novos de pedido do cliente e atualizar os valores da linha, retornando os registros selecionados (antes da atualização) para o Aplicativo lógico. As configurações de pacote/aplicativo da Conexão do DB2 são parecidas com as seguintes:

    App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;

Além disso, você pode definir um gatilho de aplicativo lógico para pesquisar, ler e remover os dados de uma tabela do DB2 usando uma operação composta Sondar Dados de API. Por exemplo, você pode ler um ou mais registros novos de pedido do cliente e excluir as linhas, retornando os registros selecionados (antes da exclusão) para o Aplicativo lógico. As configurações de pacote/aplicativo da Conexão do DB2 são parecidas com as seguintes:

    App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

Neste exemplo, o aplicativo lógico vai pesquisar, ler, atualizar e, em seguida, ler novamente os dados na tabela do DB2.

1. No quadro inicial do Azure, escolha **+** (sinal de adição), **Web + Móvel**, depois, **Aplicativo lógico**.
2. Digite o Nome (por exemplo, "ShipOrdersDb2"), Plano de Serviço de Aplicativo, outras propriedades e escolha **Criar**.
3. No quadro inicial do Azure, escolha o aplicativo lógico que você acabou de criar, **Configurações** e **Gatilhos e ações**.
4. Na folha Gatilhos e ações, escolha **Criar do Zero** em Modelos do Aplicativo lógico.
5. No painel Aplicativos de API, escolha **Conector DB2**, defina a frequência e o intervalo, e clique na **marca de seleção**.
6. No painel Aplicativos de API, selecione **Conector DB2** e expanda a lista de operações para escolher **Selecionar em NEWORDERS**.
7. Escolha a **marca de seleção** para salvar as configurações da ação e depois escolha **Salvar**. As configurações devem ter a seguinte aparência: ![][10]
8. Clique para fechar a folha **Gatilhos e ações** e, em seguida, clique para fechar a folha **Configurações**.
9. Na lista **Todas as execuções**, em **Operações**, clique no primeiro item da lista (execução mais recente).
10. Na folha **Execução do aplicativo lógico**, clique no item **AÇÃO**.
11. Na folha **Ação do aplicativo lógico**, clique no **LINK DE SAÍDAS**. As saídas devem ter a seguinte aparência: ![][11]

## Aplicativo lógico com ação do conector DB2 para a remoção de dados
Você pode definir uma ação de aplicativo lógico para remover dados de uma tabela do DB2 usando uma operação OData de Exclusão de API ou Postagem para Entidade. Por exemplo, você pode inserir um novo registro de pedido de cliente processando uma instrução SQL INSERT em uma tabela definida com uma coluna de identidade, retornando para o aplicativo lógico o valor de identidade ou as linhas afetadas (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Criar um Aplicativo lógico usando o conector DB2 para remover os dados
Você pode criar um novo Aplicativo lógico no Azure Marketplace, em seguida, usar o conector DB2 como uma ação para remover os pedidos do cliente. Por exemplo, você pode usar a operação de Exclusão condicional do conector DB2 para processar uma instrução SQL DELETE (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. No menu de hub da **Tela Inicial** do Azure, clique em **+** (sinal de adição), clique em **Web + Móvel** e **Aplicativo lógico**.
2. Na folha **Criar Aplicativo lógico**, digite um **Nome**, por exemplo, **RemoveOrdersDb2**.
3. Selecione ou defina os valores para as outras configurações (por exemplo, o plano de serviço, o grupo de recursos).
4. As configurações devem ter a seguinte aparência. Clique em **Criar**: ![][12]
5. Na folha **Configurações**, clique em **Gatilhos e ações**.
6. Na folha **Gatilhos e ações**, na lista **Modelos do Aplicativo lógico**, clique em **Criar do Zero**.
7. Na folha **Gatilhos e ações**, no painel **Aplicativos de API**, no grupo de recursos, clique em **Recorrência**.
8. Na superfície de design do aplicativo lógico, clique no item **Recorrência**, defina uma **Frequência** e **Intervalo**, por exemplo, **Dias** e **1** e clique na **Marca de seleção** para salvar as configurações de item recorrentes.
9. Na folha **Gatilhos e ações**, no painel **Aplicativos de API**, no grupo de recursos, clique em **Conector DB2**.
10. Na superfície de design do Aplicativo lógico, clique no item de ação **conector DB2**, clique nos três pontos (**...**) para expandir a lista de operações e clique em **Exclusão condicional de N**.
11. No item de ação do conector DB2, digite **ORDID ge 10000** para uma **expressão que identifica um subconjunto de entradas**.
12. Clique na **marca de seleção** para salvar as configurações da ação e depois clique em **Salvar**. As configurações devem ter a seguinte aparência: ![][13]
13. Clique para fechar a folha **Gatilhos e ações** e, em seguida, clique para fechar a folha **Configurações**.
14. Na lista **Todas as execuções**, em **Operações**, clique no primeiro item da lista (execução mais recente).
15. Na folha **Execução do aplicativo lógico**, clique no item **AÇÃO**.
16. Na folha **Ação do aplicativo lógico**, clique no **LINK DE SAÍDAS**. As saídas devem ter a seguinte aparência: ![][14]

**Observação:** o designer de aplicativo lógico trunca os nomes de tabela. Por exemplo, a operação **Exclusão condicional de NEWORDERS** será truncada para **Exclusão condicional de N**.

> [!TIP]
> Use as seguintes instruções SQL para criar a tabela de exemplo e os procedimentos armazenados.
> 
> 

Você pode criar a tabela de exemplo NEWORDERS usando as seguintes instruções DDL de SQL do DB2:

     CREATE TABLE ORDERS (  
         ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
         CUSTID INT NOT NULL ,  
         EMPID INT NOT NULL DEFAULT 10000 ,  
         ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
         REQDATE DATE DEFAULT CURRENT DATE ,  
         SHIPDATE DATE ,  
         SHIPID INT NOT NULL DEFAULT 10000,  
         FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
         SHIPNAME CHAR (40) NOT NULL ,  
         SHIPADDR CHAR (60) NOT NULL ,  
         SHIPCITY CHAR (20) NOT NULL ,  
         SHIPREG CHAR (15) NOT NULL ,  
         SHIPZIP CHAR (10) NOT NULL ,  
         SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
         PRIMARY KEY(ORDID)  
         )  

     CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



Você pode criar o exemplo de procedimento armazenado SPOERID usando as seguintes instruções DDL de SQL do DB2:

     CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
         DYNAMIC RESULT SETS 1  
     P1: BEGIN  
         DECLARE CURSOR1 CURSOR WITH RETURN FOR  
             SELECT * FROM NWIND.NEWORDERS  
                 WHERE ORDID = ORDERID;  
         OPEN CURSOR1;  
     END P1  
     ') 


## Configuração Híbrida (opcional)
> [!NOTE]
> Essa etapa será requerida apenas se você estiver usando o conector DB2 localmente protegido por firewall.
> 
> 

O Serviço de Aplicativo usa o Gerenciador de Configuração Híbrida para se conectar com segurança ao sistema local. Se o conector usar um Servidor DB2 da IBM local para Windows, o Gerenciador de Conexões Híbridas será requerido.

Consulte [Usando o Gerenciador de Conexão Híbrida](app-service-logic-hybrid-connection-manager.md).

## Fazer mais com seu conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos lógicos?](app-service-logic-what-are-logic-apps.md).

Crie aplicativos de API usando APIs REST. Consulte [Referência a aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

<!---HONumber=AcomDC_0803_2016-->