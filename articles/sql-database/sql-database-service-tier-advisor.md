<properties 
   pageTitle="Recomendações de camada de preços do Banco de Dados SQL do Azure" 
   description="Ao alterar as camadas de preços no portal do Azure, são fornecidas recomendações de camada de preços que apontam a camada mais adequada para executar a carga de trabalho do Banco de Dados SQL do Azure. As camadas de preços descrevem o nível de serviço e o nível de desempenho de um banco de dados SQL." 
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
   ms.date="12/01/2015"
   ms.author="sstein"/>

# Recomendações de camada de preços do Banco de Dados SQL

 São fornecidas recomendações tipo de preço que recomendam a camada de serviços e o nível de desempenho mais adequados para executar a carga de trabalho do banco de dados SQL do Azure.

> [AZURE.NOTE]As recomendações de tipo de preço estão disponíveis apenas para bancos de dados Web e Business e pools de banco de dados elástico, e apenas no [Portal do Azure](https://portal.azure.com/).


Obtenha as recomendações de tipo de preço durante as seguintes tarefas:

- [Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL](sql-database-scale-up.md)
- [Atualizar servidor do SQL Azure para V12](sql-database-v12-upgrade.md)
- Navegue até seu servidor V12 - se os bancos de dados puderem se [beneficiar de um pool de banco de dados elástico](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools), a folha do servidor exibirá uma mensagem indicando um pool recomendado. Clique na mensagem para criar o pool recomendado.
- [Criar um pool de banco de dados elástico](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## Visão geral

O serviço do Banco de Dados SQL analisa os requisitos atuais de desempenho e recursos avaliando o uso histórico de recursos de um banco de dados SQL. Além disso, a camada de serviço mínima aceitável é determinada com base no tamanho do banco de dados e nos recursos de [continuidade dos negócios](sql-database-business-continuity.md) habilitados.

Com a análise dessas informações, são recomendados a camada de serviço e o nível de desempenho mais adequados para execução da carga de trabalho comum do banco de dados e manutenção do respectivo conjunto de recursos atuais.

- O serviço examina de 15 a 30 dias anteriores dos dados históricos (uso de recursos, tamanho do banco de dados e atividade do banco de dados) e faz uma comparação entre a quantidade de recursos consumidos e as reais limitações das camadas de serviço e dos níveis de desempenho atualmente disponíveis
- Os dados são analisados em intervalos de 15 segundos e cada resultset do intervalo é classificado na camada de serviço e no nível de desempenho existentes mais adequados para tratamento da carga de trabalho do resultset.
- Essas amostras de 15 segundos são agregadas à análise mais ampla de 15 a 30 dias, sendo assim recomendados a camada de serviço e o nível de desempenho que podem tratar de forma mais eficiente 95% da carga de trabalho histórica.

### Recomendações

Com base no uso do banco de dados, atualmente, podemos encontrar 2 categorias de recomendações:


| Recomendações | Descrição |
| :--- | :--- |
| Atualizar | Atualize para uma nova camada. |
| Indisponível | Um banco de dados exige uma carga de trabalho mínima ou, aproximadamente, 14 dias de atividade. Não há dados suficientes para fornecer uma recomendação válida. |

## Obtendo recomendações de camada de preços

Obtenha recomendações de camada de preços selecionando um banco de dados Web ou Business e clicando no bloco **Camada de preços**. (As recomendações de tipo de preço também estarão disponíveis quando você [Atualizar o Azure SQL Server para o V12](sql-database-v12-upgrade.md).)

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **PROCURAR** > **bancos de dados SQL**.
4. Na folha **Bancos de Dados SQL**, clique no banco de dados para o qual você deseja ver uma recomendação:

    ![Selecionar um banco de dados][1]

5. Na folha do banco de dados, selecione o bloco **Camada de preços**.

    ![Camada de preços][2]


7. Depois de clicar no bloco **Camada de preços**, será exibida a folha **Camadas de preços recomendadas**, onde você pode clicar na camada sugerida e no botão **Selecionar** para alterar para a camada em questão.

    ![Inscrever-se para obter a visualização][4]

8. Se preferir, clique em **Exibir detalhes de uso** para abrir a folha **Detalhes de Recomendação da Camada de Preços**, onde você pode exibir a camada recomendada para o banco de dados, uma comparação de recursos entre as camadas atuais e recomendadas e um gráfico da análise do uso histórico de recursos.

    ![Inscrever-se para obter a visualização][5]



## Resumo

As recomendações de camada de preços proporcionam uma experiência automatizada de coleta de dados de telemetria para cada banco de dados SQL, bem como apontam a melhor combinação de nível de desempenho/camada de serviço com base nos requisitos de recursos e nas necessidades reais de desempenho de um banco de dados. Clique no bloco **Camada de preços** em uma folha de banco de dados para ver as recomendações de camada de preços.



## Próximas etapas

Dependendo dos detalhes do seu banco de dados específico, a execução de uma atualização ou um downgrade normalmente não acontece de forma instantânea. O portal fornecerá notificações à medida que o banco de dados faz a transição para sua nova camada, ou você pode monitorar o status da atualização consultando a exibição [sys.dm\_operation\_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270022.aspx) no banco de dados mestre do Servidor de Banco de Dados SQL.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[2]: ./media/sql-database-service-tier-advisor/pricing-tier.png
[3]: ./media/sql-database-service-tier-advisor/preview-sign-up.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<!---HONumber=AcomDC_1203_2015-->