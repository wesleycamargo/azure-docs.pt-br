<properties 
   pageTitle="Recomendações de camada de preços do Banco de Dados SQL do Azure" 
   description="Ao alterar as camadas de preços no portal do Azure, são fornecidas recomendações de camada de preços que apontam a camada mais adequada para executar a carga de trabalho do Banco de Dados SQL do Azure." 
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
   ms.date="05/18/2015"
   ms.author="sstein"/>

# Recomendações de camada de preços do Banco de Dados SQL

 Ao alterar as camadas de preços no portal do Azure, são fornecidas recomendações de camada de preços que apontam a camada mais adequada para executar a carga de trabalho do Banco de Dados SQL do Azure.

> [AZURE.NOTE]As recomendações de camada de preços estão disponíveis apenas para bancos de dados Web e Business e apenas no [Portal do Azure](https://portal.azure.com/).


## Visão geral

O Azure analisa os requisitos atuais de desempenho e recursos avaliando o uso histórico de recursos de um Banco de Dados SQL. Além disso, a camada de serviço mínima aceitável é determinada com base no tamanho do banco de dados e nos recursos de [continuidade dos negócios](https://msdn.microsoft.com/library/azure/hh852669.aspx) habilitados.

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

Obtenha recomendações de camada de preços selecionando um banco de dados Web ou Business e clicando no bloco **Camada de preços**.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **PROCURAR** no menu à esquerda.
3. Clique em **Bancos de dados SQL** na folha **Procurar**.
4. Na folha **Bancos de dados SQL**, clique no banco de dados que deseja que o serviço analise.

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

Dependendo dos detalhes do seu banco de dados específico, a execução de uma atualização ou um downgrade normalmente não acontece de forma instantânea. O portal de gerenciamento fornecerá notificações à medida que o banco de dados faz a transição para sua nova camada, ou você pode monitorar o status da atualização consultando a exibição [sys.dm_operation_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270022.aspx) no banco de dados mestre do Servidor do Banco de Dados SQL.


<!--Image references-->
[1]: ./media/sql-database-pricing-tier-recommendations/select-database.png
[2]: ./media/sql-database-pricing-tier-recommendations/pricing-tier.png
[3]: ./media/sql-database-pricing-tier-recommendations/preview-sign-up.png
[4]: ./media/sql-database-pricing-tier-recommendations/choose-pricing-tier.png
[5]: ./media/sql-database-pricing-tier-recommendations/usage-details.png


 

<!---HONumber=62-->