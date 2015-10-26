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
   ms.date="09/22/2015"
   ms.author="sstein"/>

# Consultor de Índices de Banco de Dados SQL

O Consultor de Índices de banco de dados SQL Azure recomenda novos índices para seus Bancos de Dados SQL existentes a fim de melhorar o desempenho da consulta atual.

O serviço de Banco de Dados SQL avalia o desempenho do índice, analisando o uso do recurso de histórico para um Banco de Dados SQL e os índices que são mais adequados para a execução de carga de trabalho normal do banco de dados são recomendados.

O Consultor de Índices facilita o gerenciamento de índices, fornecendo recomendações sobre quais índices criar. Para servidores V12, o Consultor de Índices também pode criar e validar os índices com apenas alguns cliques no [Portal de Visualização do Azure](https://portal.azure.com/). Depois que o índice é criado, o serviço de Banco de Dados SQL analisa o desempenho da carga de trabalho de banco de dados e fornece detalhes do impacto do novo índice. Se a análise determinar que um índice recomendado tem um impacto negativo no desempenho, o índice é revertido automaticamente.

Consultor de Índices permite que você gaste menos tempo ajustando o desempenho do banco de dados.


> [AZURE.NOTE]O Consultor de Índices está atualmente em visualização e está disponível somente no [Portal de Visualização do Azure](https://portal.azure.com/).


## Considerações de visualização

O Consultor de Índices está atualmente em visualização e tem as seguintes limitações:

- Recomendações de índices podem ser criadas e validadas automaticamente apenas para servidores V12 (scripts de recomendações e criação de índice são fornecidos para servidores V12).
- Recomendações e gerenciamento estão disponíveis apenas para índices não clusterizados.

## Pré-requisitos

Para exibir e criar recomendações de índice, você precisa das permissões corretas ao [controle de acesso baseado em função](role-based-access-control-configure.md) no Azure.

- As permissões de **Leitor** e **Colaborador do Banco de Dados SQL** são necessárias para exibir as recomendações.
- As permissões de **Proprietário** e **Colaborador do Banco de Dados SQL** são necessárias para executar quaisquer ações: criar ou descartar índices e cancelar a criação do índice.


## Uso do Consultor de Índices

É muito fácil usar o Consultor de Índices. Para simplificar o gerenciamento de índices do banco de dados, siga estas orientações:

- Primeiro, examine a lista de recomendações de índices e decida quais índices devem ser criados ou ignorados. A lista de recomendações é classificada e rotulada pelo seu impacto de desempenho estimado (detalhes abaixo). 
- Crie ou ignore os índices recomendados. Crie automaticamente o índice clicando em **Criar Índice** no portal ou manualmente executando o script de criação de índice.
- Para índices criados manualmente, você deve monitorar o processo de criação e medir o impacto de desempenho. Para índices criados automaticamente, a análise de impacto de desempenho e monitoramento é executada automaticamente pelo serviço de Banco de Dados SQL. 



## Examinar índices recomendados

O Consultor de Índices fornece uma lista de recomendações de índices na folha de Banco de Dados no [Portal de Visualização do Azure](https://portal.azure.com/). As principais recomendações selecionadas são mostradas para cada tabela no banco de dados selecionado, no qual a criação um novo índice pode proporcionar ganhos de desempenho.

### Para examinar as recomendações de índice disponíveis no momento:

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com/).
2. Clique em **PROCURAR** no menu à esquerda.
3. Clique em **Bancos de dados SQL** na folha **Procurar**.
4. Na folha **Bancos de Dados SQL**, clique no banco de dados cujos índices recomendados você deseja examinar.
5. Clique em **Consultor de Índices** para abrir e exibir as **Recomendações de índices** disponíveis para o banco de dados selecionado.

> [AZURE.NOTE]Para obter recomendações de índice, um banco de dados precisa ter aproximadamente uma semana de uso e dentro dessa semana deve haver alguma atividade. Também é necessário haver certa atividade consistente. O Consultor de Índices pode otimizar com maior facilidade padrões de consulta consistentes do que intermitências aleatórias e irregulares de atividade.

![Índices recomendados][3]

As recomendações são classificadas de acordo com seu impacto em potencial no desempenho nas seguintes quatro categorias:

| Impacto | Descrição |
| :--- | :--- |
| Alto | Recomendações de alto impacto devem fornecer o impacto mais significativo no desempenho. |
| Substancial | Recomendações de impacto substancial devem melhorar o desempenho visivelmente. |
| Moderado | Recomendações de impacto moderado devem melhorar o desempenho, mas não substancialmente. |
| Baixo | Recomendações de baixo impacto devem fornecer um desempenho melhor do que sem o índice, mas as melhorias podem não ser significativas. 
Use a marca de Impacto para determinar os melhores candidatos para a criação de novos índices.

### Gerenciar a lista de índices recomendados

Para os casos em que a lista de índices recomendados contiver índices que você achar que não serão benéficos, o Consultor de Índices permite descartar recomendações de índice (você pode adicionar índices descartados de volta para os **Índices recomendados** posteriormente, se necessário).

#### Descartar uma recomendação de índice

1. Selecione o índice na lista de **Índices recomendados**.
1. Clique em **Descartar índice** na folha **Detalhes do índice**.

#### Exibir índices descartados e adicioná-los de volta à lista principal

1. Na folha **Recomendações de índice**, clique em **Exibir recomendações de índices descartadas**.
1. Selecione um índice descartado na lista para exibir seus detalhes.
1. Outra alternativa é clicar em **Desfazer descarte** para adicionar o índice de volta à lista principal de **Recomendações de índices**.



## Criar novos índices

O Consultor de Índices concede total controle sobre a maneira como os índices são criados. Cada recomendação fornece um script de criação de índice do T-SQL e você pode examinar os detalhes exatos de como o índice será criado antes de qualquer ação ser tomada em um banco de dados.

As recomendações de índices estão disponíveis para todos os servidores de banco de dados SQL, mas somente os servidores V12 recebem criação de índice automatizada. Servidores não V12 ainda podem se beneficiar com o Consultor de Índices, mas você precisará criar os índices manualmente conforme descrito abaixo.

Tanto para a criação automática quanto manual, basta selecionar um índice recomendado na folha **Recomendações de índices** e fazer o seguinte:

### Criação de índices automática (somente servidores V12)

Se o banco de dados estiver em um servidor V12, você poderá criar facilmente um índice recomendado selecionando o índice desejado no portal e clicando em **Criar Índice**.

O banco de dados permanece online durante a criação do índice. Usar o Consultor de Índices para criar um índice não deixa o banco de dados offline.

Além disso, os índices criados com **Criar Índice** não requerem nenhum monitoramento de desempenho adicional. Se o índice tiver um impacto negativo no desempenho, ele será revertido automaticamente. Depois de usar Criar Índice, as métricas sobre o impacto do novo índice estarão disponíveis no portal.


### Criação de índices manual (todos os servidores)

Selecione qualquer índice recomendado no portal e clique em **Exibir Script**. Execute esse script em seu banco de dados para criar o índice recomendado. Índices criados manualmente não são monitorados e validados para avaliar o impacto no desempenho real, portanto é recomendável que você monitore esses índices após a criação para verificar se eles fornecem ganhos de desempenho e ajustá-los ou excluí-los se necessário. Para obter detalhes sobre a criação de índices, consulte [CRIAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Cancelar a criação do índice

Índices que estão em um status **Pendente** podem ser cancelados. Índices que estão sendo criados (status **Em execução**) não podem ser cancelados.

1. Selecione qualquer índice **Pendente** na área **Operações de índice** para abrir a folha **Detalhes do índice**.
1. Clique em **Cancelar** para anular o processo de criação de índice.

## Monitorar as operações do índice após sua criação

A criação de um índice não acontece instantaneamente. O portal fornece detalhes sobre o status das operações do índice. Ao gerenciar índices, eles poderão estar em um dos estados a seguir:

| Status | Descrição |
| :--- | :--- |
| Pendente | O comando de criação de índice foi recebido e o índice está programado para criação. |
| Executando | O comando de criação de índice está em execução e o índice está sendo criado no momento. |
| Sucesso | O índice foi criado com êxito. |
| Falha | O índice não foi criado. Este pode ser um problema temporário ou, possivelmente, uma alteração de esquema na tabela, tornando o script inválido. |
| Revertendo | O processo de criação de índice foi cancelado ou foi considerado não funcional e está sendo revertido automaticamente. |



![Índices recomendados][4]



## Remover um índice
Você pode remover os índices que foram criados com o Consultor de Índices.


1. Selecione um índice criado com êxito na lista de **Operações de índice**.
1. Clique em **Remover índice** na folha **Detalhes do índice** ou clique em **Exibir Script** para um script DROP INDEX.



## Resumo

As recomendações de índices fornecem uma experiência automatizada para gerenciar a criação de índices e a análise para cada banco de dados SQL, recomendando os melhores índices. Clique no bloco **Consultor de Índices** em uma folha de banco de dados para ver as recomendações de índices.



## Próximas etapas

Monitore suas recomendações de índices e continue a aplicá-las para aprimorar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Consultor de Índices continuará a monitorar e recomendar índices que podem potencialmente melhorar o desempenho do seu banco de dados.


<!--Image references-->
[1]: ./media/sql-database-index-advisor/index-recommendations.png
[2]: ./media/sql-database-index-advisor/index-details.png
[3]: ./media/sql-database-index-advisor/recommended-indexes.png
[4]: ./media/sql-database-index-advisor/index-operations.png

<!---HONumber=Oct15_HO3-->