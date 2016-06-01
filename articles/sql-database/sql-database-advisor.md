<properties 
   pageTitle="Advisor do Banco de Dados SQL do Azure" 
   description="O Advisor do Banco de Dados SQL do Azure fornece recomendações para seus Bancos de Dados SQL existentes que podem melhorar o desempenho de consulta atual." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="05/10/2016"
   ms.author="sstein"/>

# Advisor do Banco de Dados SQL

O Index Advisor do Banco de Dados SQL do Azure foi atualizado para fornecer recomendações não apenas para criação e remoção de índices, mas agora inclui também recomendações para parametrizar consultas e corrigir problemas do esquema. Com essas recomendações adicionais, o Index Advisor é agora o **Advisor do Banco de Dados SQL**.

O Advisor do Banco de Dados SQL avalia o desempenho ao analisar seu o histórico de uso do banco de dados SQL. As recomendações mais adequadas para executar a carga de trabalho normal do seu banco de dados são recomendadas.

As recomendações a seguir estão disponíveis para servidores V12 (as recomendações não estão disponíveis para servidores V11). Atualmente, você pode definir as recomendações de criação e remoção de índice para que sejam aplicadas automaticamente, consulte [Gerenciamento automático de índices](#enable-automatic-index-management) abaixo para obter detalhes.

## Criar recomendações de índice 

Recomendações para **Criar Índice** aparecem quando o serviço de Banco de Dados SQL detecta um índice ausente que, se criado, pode beneficiar sua carga de trabalho de bancos de dados (somente índices não clusterizados).


## Recomendações para Remover Índice

Recomendações para **Remover Índice** aparecem quando o serviço de Banco de Dados SQL detecta índices duplicados (atualmente em preview e aplica-se somente a índices duplicados).

## Recomendações para parametrizar consultas

Recomendações para **Parametrizar consultas** aparecem quando o serviço de Banco de Dados SQL detecta que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. Isso abre uma oportunidade de aplicar parametrização forçada, o que permitirá que os planos de consulta sejam armazenados em cache e reutilizados no futuro, melhorando o desempenho e reduzindo o uso de recursos.

## Recomendações para corrigir problemas do esquema

Recomendações para **Corrigir problemas do esquema** aparecem quando o serviço de Banco de Dados SQL observa uma anomalia no número erros de SQL relacionados ao esquema ocorrendo no Banco de Dados SQL do Azure. Essa recomendação normalmente aparece quando seu banco de dados encontra vários erros relacionados ao esquema (nome da coluna inválido, nome do objeto inválido, etc.) no período de uma hora.


## Exibindo recomendações

A página de recomendações é onde você pode exibir as principais recomendações com base no seu impacto potencial para melhorar o desempenho. Você também pode exibir o status das operações do histórico. Selecione um status ou recomendação para ver seus detalhes.

Para exibir e aplicar recomendações, você precisa das permissões corretas ao [controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) no Azure. As permissões de **Leitor** e **Contribuidor do DB SQL** são necessárias para exibir as recomendações, enquanto as permissões de **Proprietário** e **Colaborador do Banco de Dados SQL** são necessárias para executar quaisquer ações: criar ou remover índices e cancelar a criação de índices.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **PROCURAR** > **Bancos de Dados SQL** e selecione o banco de dados.
5. Clique em **Todas as configurações** > **Recomendações** para exibir as **Recomendações** disponíveis para o banco de dados selecionado.

> [AZURE.NOTE] Para obter recomendações, um banco de dados precisa ter aproximadamente uma semana de uso e dentro dessa semana deve haver alguma atividade. Também é necessário haver certa atividade consistente. O Advisor do Banco de Dados SQL pode otimizar com maior facilidade padrões de consulta consistentes do que intermitências aleatórias e irregulares de atividade. Se não houver recomendações, a página **Recomendações** deverá fornecer uma mensagem explicando o motivo.

![Recomendações](./media/sql-database-index-advisor/recommendations.png)

As recomendações são classificadas de acordo com seu impacto em potencial no desempenho nas seguintes quatro categorias:

| Impacto | Descrição |
| :--- | :--- |
| Alto | Recomendações de alto impacto devem fornecer o impacto mais significativo no desempenho. |
| Média | Recomendações de médio impacto devem melhorar o desempenho, mas não substancialmente. |
| Baixo | Recomendações de baixo impacto devem fornecer um desempenho melhor do que seria obtido sem elas, mas as melhorias podem não ser significativas. 


### Removendo recomendações da lista

Se sua lista de recomendações contiver itens que você deseja remover da lista, você poderá descartar a recomendação:

1. Selecione uma recomendação na lista de **Recomendações**.
2. Clique em **Descartar** na folha **Detalhes**.


Se desejar, você poderá adicionar itens descartados de volta para a lista de **Recomendações**:

1. Na folha **Recomendações**, clique em **Exibir descartados**.
1. Selecione um item descartado na lista para exibir seus detalhes.
1. Outra alternativa é clicar em **Desfazer Descarte**, para adicionar o índice de volta à lista principal de **Recomendações**.



## Aplicando recomendações

O Advisor do Banco de Dados SQL concede a você controle total sobre como as recomendações são habilitadas usando qualquer uma das três opções abaixo.

- Aplicar uma recomendação individual de cada vez.
- Habilitar o Advisor a aplicar as recomendações automaticamente (atualmente, aplica-se somente às recomendações de índice).
- Executar manualmente o script T-SQL recomendado no banco de dados para implementar uma recomendação.

Selecione qualquer recomendação para exibir seus detalhes e, em seguida, clique em **Exibir script** para examinar os detalhes exatos de como a recomendação será criada.

O banco de dados permanece online enquanto o supervisor aplica a recomendação – o uso do Advisor do Banco de Dados SQL nunca colocará um banco de dados offline.

### Aplicar uma recomendação individual

Você pode examinar e aceitar uma recomendação de cada vez.

1. Na folha **Recomendações**, clique em uma recomendação.
2. Na folha **Detalhes**, clique em **Aplicar**.

    ![Aplicar recomendação](./media/sql-database-index-advisor/apply.png)


### Habilitar o gerenciamento de índice automático

Você pode definir o Advisor do Banco de Dados SQL para implementar as recomendações automaticamente. Conforme as recomendações são disponibilizadas, elas serão aplicadas automaticamente. Como com todas as operações de índice gerenciadas pelo serviço, se o impacto de desempenho for negativo, a recomendação será revertida.

1. Na folha **Recomendações**, clique em **Automatizar**:

    ![Configurações do supervisor](./media/sql-database-index-advisor/settings.png)

2. Configurar o Advisor para **Criar** ou **Remover** índices automaticamente:

    ![Índices recomendados](./media/sql-database-index-advisor/automation.png)




### Executar manualmente o script T-SQL recomendado

Selecione qualquer recomendação e, em seguida, clique em **Exibir script**. Execute este script em seu banco de dados para aplicar manualmente a recomendação.

*Os índices que são executados manualmente não são monitorados e validados para impacto no desempenho pelo serviço*. Portanto, é recomendável que você monitore esses índices após a criação para verificar se eles fornecem ganhos de desempenho e ajustá-los ou excluí-los, se necessário. Para obter detalhes sobre a criação de índices, consulte [CRIAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Cancelando recomendações

Recomendações que estão com status **Pendente**, **Verificando**, ou **Sucesso** podem ser canceladas. Recomendações com status **Executando** não podem ser canceladas.

1. Selecione uma recomendação na área **Histórico de Ajustes** para abrir a folha **detalhes da recomendação**.
2. Clique em **Cancelar** para anular o processo de aplicação da recomendação.



## Monitoramento de operações

A aplicação de uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o status das operações de recomendação. Um índice pode estar em um dos estados a seguir:

| Status | Descrição |
| :--- | :--- |
| Pendente | O comando Aplicar recomendação foi recebido e está programado para execução. |
| Executando | A recomendação está sendo aplicada. |
| Sucesso | A recomendação foi aplicada com êxito. |
| Erro | Ocorreu um erro durante o processo de aplicação da recomendação. Este pode ser um problema temporário ou, possivelmente, uma alteração de esquema na tabela, tornando o script inválido. |
| Revertendo | A recomendação foi aplicada, mas foi considerada não funcional e está sendo revertida automaticamente. |
| Revertida | A recomendação foi revertida. |

Clique em uma recomendação no processo da lista para ver seus detalhes:

![Índices recomendados](./media/sql-database-index-advisor/operations.png)



### Revertendo uma recomendação

Se você usou o supervisor para aplicar a recomendação (ou seja, você não executou o script T-SQL manualmente), ele a reverterá automaticamente se o impacto de desempenho for negativo. Se, por algum motivo, você simplesmente desejar reverter uma recomendação, poderá fazer o seguinte:


1. Selecione uma recomendação aplicada com êxito na área **Histórico de ajustes**.
2. Clique em **Revert** na folha **detalhes da recomendação**.

![Índices recomendados](./media/sql-database-index-advisor/details.png)


## Monitorando o impacto do desempenho de recomendações de índice

Depois que as recomendações forem implementadas com êxito (atualmente, apenas recomendações de operações de índice e de parametrização de consultas), clique em **Análise de Consultas** na folha detalhes da recomendação para abrir o [Análise de Desempenho de Consultas](sql-database-query-performance.md) e ver o impacto no desempenho das principais consultas.

![Monitorar o impacto do desempenho](./media/sql-database-index-advisor/query-insights.png)



## Resumo

O Advisor do Banco de Dados SQL fornece recomendações para aprimorar o desempenho de bancos de dados SQL. Ao fornecer scripts T-SQL, além de individuais e totalmente automáticos (atualmente, somente de índice), o Advisor fornece assistência útil ao otimizar seus bancos de dados e, como resultado final, melhorar o desempenho de consulta.



## Próximas etapas

Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Advisor do Banco de Dados SQL continuará a monitorar e fornecer recomendações que podem potencialmente melhorar o desempenho do seu banco de dados.

<!---HONumber=AcomDC_0518_2016-->