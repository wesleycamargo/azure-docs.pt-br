<properties 
   pageTitle="Consultor de Índices do Banco de Dados SQL do Azure" 
   description="O Consultor de Índices de banco de dados SQL Azure recomenda novos índices para seus Bancos de Dados SQL existentes a fim de melhorar o desempenho da consulta atual." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="01/23/2016"
   ms.author="sstein"/>

# Consultor de Índices de Banco de Dados SQL

O Index Advisor do banco de dados SQL do Azure fornece recomendações de índices para seus bancos de dados SQL existentes que podem melhorar o desempenho da consulta atual. O serviço de banco de dados SQL avalia o desempenho do índice ao analisar o histórico de uso do banco de dados SQL. Os índices que são mais adequados para executar a carga de trabalho normal do banco de dados são recomendados.

O Index Advisor ajuda você a ajustar o desempenho do banco de dados ao:

- fornecer recomendações sobre quais índices criar (as recomendações estão disponíveis apenas para índices não clusterizados).
- fornecer recomendações sobre quais índices remover (as recomendações para remover índice estão em preview e são aplicáveis somente para duplicar índices).
- permitir que você aceite aplicar as recomendações de índice automaticamente sem qualquer interação do usuário. (As recomendações automatizadas exigem que o [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx) esteja habilitado e em execução.)
- reverter automaticamente as recomendações que tenham um impacto negativo no desempenho. 


Este artigo descreve o Index Advisor para servidores V12. As recomendações de índice estão disponíveis para servidores V11, mas você deve executar o script Transact-SQL (T-SQL) fornecido para implementar a recomendação. O supervisor não reverterá as operações de índice nos servidores V11. Então, você deve monitorar e reverter o impacto no desempenho conforme necessário.


### Permissões

Para exibir e criar recomendações de índice, você precisa das permissões corretas ao [controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) no Azure.

- As permissões de **Leitor** e **Colaborador do Banco de Dados SQL** são necessárias para exibir as recomendações.
- As permissões de **Proprietário** e **Colaborador do Banco de Dados SQL** são necessárias para executar quaisquer ações: criar ou descartar índices e cancelar a criação do índice.


## Exibindo as recomendações de índice

A página de recomendações de índice é o local no qual você pode exibir os principais índices sugeridos com base no impacto potencial para melhorar o desempenho. Você também pode exibir o status de várias operações de índice mais recentes. Selecione um status ou recomendação para ver seus detalhes.

Para exibir as recomendações de índice:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **PROCURAR** > **Bancos de Dados SQL** e selecione o banco de dados.
5. Clique em **Todas as configurações** > **Index Advisor** para exibir as **Recomendações de índices** disponíveis para o banco de dados selecionado.

> [AZURE.NOTE] Para obter recomendações de índice, um banco de dados precisa ter aproximadamente uma semana de uso e dentro dessa semana deve haver alguma atividade. Também é necessário haver certa atividade consistente. O Consultor de Índices pode otimizar com maior facilidade padrões de consulta consistentes do que intermitências aleatórias e irregulares de atividade. Se não houver recomendações, a página **Recomendações de índice** deverá fornecer uma mensagem explicando o motivo.

![Índices recomendados](./media/sql-database-index-advisor/recommendations.png)

As recomendações são classificadas de acordo com seu impacto em potencial no desempenho nas seguintes quatro categorias:

| Impacto | Descrição |
| :--- | :--- |
| Alto | Recomendações de alto impacto devem fornecer o impacto mais significativo no desempenho. |
| Substancial | Recomendações de impacto substancial devem melhorar o desempenho visivelmente. |
| Moderado | Recomendações de impacto moderado devem melhorar o desempenho, mas não substancialmente. |
| Baixo | Recomendações de baixo impacto devem fornecer um desempenho melhor do que sem o índice, mas as melhorias podem não ser significativas. 
Use a marca de Impacto para determinar os melhores candidatos para a criação de novos índices.


### Removendo recomendações de índice da lista

Se sua lista de índices recomendados contiver índices que você deseja remover da lista, você pode descartar a recomendação:

1. Selecione uma recomendação na lista de **índices recomendados**.
2. Clique em **Descartar índice** na folha **Detalhes do índice**.


Se desejar, você pode adicionar índices descartados de volta para a lista de **índices recomendados**:

1. Na folha **Recomendações de índice**, clique em **Exibir recomendações de índices descartadas**.
1. Selecione um índice descartado na lista para exibir seus detalhes.
1. Outra alternativa é clicar em **Desfazer descarte** para adicionar o índice de volta à lista principal de **Recomendações de índices**.



## Aplicando recomendações de índice

O Index Advisor concede controle total sobre como as recomendações de índice são habilitadas usando qualquer uma das três opções abaixo.

- Aplicar uma recomendação individual de cada vez.
- Habilitar o Index Advisor para aplicar as recomendações de índice automaticamente.
- Executar manualmente o script T-SQL recomendado no banco de dados para implementar uma recomendação.

Selecione qualquer recomendação para exibir seus detalhes e, em seguida, clique em **Exibir script** para examinar os detalhes exatos de como a recomendação será criada.

O banco de dados permanece online enquanto o supervisor aplica a recomendação – o uso do Index Advisor nunca colocará um banco de dados offline.

### Aplicar uma recomendação individual

Você pode examinar e aceitar uma recomendação de cada vez.

1. Na folha **recomendações do Índice**, clique em uma recomendação.
2. Na folha **detalhes do Índice**, clique em **Aplicar**.

    ![Aplicar recomendação](./media/sql-database-index-advisor/apply.png)


### Habilitar o gerenciamento de índice automático

Você pode definir o Index Advisor para implementar as recomendações automaticamente. Conforme as recomendações são disponibilizadas, elas serão aplicadas automaticamente. Como com todas as operações de índice gerenciadas pelo serviço, se o impacto de desempenho for negativo, a recomendação será revertida.

1. Na folha **recomendações do índice**, clique em **Configurações do Supervisor**:

    ![Configurações do supervisor](./media/sql-database-index-advisor/settings.png)

2. Definir o supervisor para **Criar** ou **Remover** índices automaticamente:

    ![Índices recomendados](./media/sql-database-index-advisor/automation.png)




### Executar manualmente o script T-SQL recomendado

Selecione qualquer recomendação e, em seguida, clique em **Exibir script**. Execute este script em seu banco de dados para aplicar manualmente a recomendação.

*Os índices que são executados manualmente não são monitorados e validados para impacto no desempenho pelo serviço*. Portanto, é recomendável que você monitore esses índices após a criação para verificar se eles fornecem ganhos de desempenho e ajustá-los ou excluí-los, se necessário. Para obter detalhes sobre a criação de índices, consulte [CRIAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Cancelar a criação do índice

Índices que estão em um status **Pendente** podem ser cancelados. Índices que estão sendo criados (status **Em execução**) não podem ser cancelados.

1. Selecione qualquer índice **Pendente** na área **Operações de índice** para abrir a folha **Detalhes do índice**.
2. Clique em **Cancelar** para anular o processo de criação de índice.



## Monitorando as operações de índice

A aplicação de uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o status das operações do índice. Ao gerenciar índices, eles poderão estar em um dos estados a seguir:

| Status | Descrição |
| :--- | :--- |
| Pendente | O comando de criação de índice foi recebido e o índice está programado para criação. |
| Executando | O comando de criação de índice está em execução e o índice está sendo criado no momento. |
| Sucesso | O índice foi criado com êxito. |
| Falha | O índice não foi criado. Este pode ser um problema temporário ou, possivelmente, uma alteração de esquema na tabela, tornando o script inválido. |
| Revertendo | O processo de criação de índice foi cancelado ou foi considerado não funcional e está sendo revertido automaticamente. |

Clique em uma recomendação no processo da lista para ver seus detalhes:

![Índices recomendados](./media/sql-database-index-advisor/operations.png)



### Revertendo um índice

Se você usou o supervisor para criar um índice (ou seja, você não executou o script T-SQL manualmente), ele reverterá automaticamente o índice se o impacto de desempenho for negativo. Se por algum motivo, você simplesmente desejar reverter uma operação do Index Advisor, você pode fazer o seguinte.


1. Selecione um índice criado com êxito na lista de **Operações de índice**.
2. Clique em **Reverter** na folha **Detalhes do índice** ou clique em **Exibir Script** para um script DROP INDEX que você pode executar.

![Índices recomendados](./media/sql-database-index-advisor/details.png)


## Monitorando o impacto do desempenho de recomendações de índice

Depois que as recomendações forem implementadas com êxito, clique em **Consultar Insights** na folha Detalhes de Índice para abrir o [Query Performance Insights](sql-database-query-performance.md) e ver o impacto no desempenho das principais consultas.

![Monitorar o impacto do desempenho](./media/sql-database-index-advisor/query-insights.png)


## Resumo

O Index Advisor fornece recomendações de índice e uma experiência automatizada para gerenciar índices de banco de dados SQL. Ao fornecer scripts T-SQL, bem como opções de gerenciamento de índice individuais e totalmente automáticas, o Index Advisor fornece assistência útil ao otimizar seus índices de bancos de dados e, por fim, melhorar o desempenho de consulta.



## Próximas etapas

Monitore suas recomendações de índices e continue a aplicá-las para aprimorar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Consultor de Índices continuará a monitorar e recomendar índices que podem potencialmente melhorar o desempenho do seu banco de dados.

<!---HONumber=AcomDC_0224_2016-->