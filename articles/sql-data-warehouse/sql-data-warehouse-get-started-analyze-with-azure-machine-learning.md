<properties
   pageTitle="Analisar dados com o Aprendizado de Máquina do Azure | Microsoft Azure"
   description="Tutorial para usar o Aprendizado de Máquina do Azure com o Data Warehouse do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="sahajs"/>

# Analisar dados com o Aprendizado de Máquina do Azure
Este tutorial mostrará como criar um modelo de aprendizado de máquina preditivo com o Aprendizado de Máquina do Azure usando seus dados do SQL Data Warehouse do Azure. Neste tutorial, usaremos o banco de dados do AdventureWorksDW e classificaremos os clientes da oficina de bicicletas Adventure Works como compradores de bicicleta ou não, para criar uma campanha de marketing direcionada.


## Pré-requisitos
Para acompanhar este tutorial, você precisará

- SQL Data Warehouse com o banco de dados de exemplo do AdventureWorksDW.

[Criar um SQL Data Warehouse][] mostra como provisionar um banco de dados com dados de exemplo. Se você já tiver um banco de dados do SQL Data Warehouse, mas não tiver dados de exemplo, é possível [carregar dados de exemplo manualmente][]


## Etapa 1: obter dados 
Leremos os dados da exibição dbo.vTargetMail no banco de dados do AdventureWorksDW.

1. Entre no [Estúdio de Aprendizado de Máquina do Azure][] e clique em Meus Testes.
2. Clique em **+NOVO** e selecione **Teste em Branco**.
3. Insira um nome para o seu teste: Marketing Direcionado.
4. Arraste o módulo **Leitor** do painel de módulos na tela.
5. Especifique os detalhes do seu banco de dados do SQL Data Warehouse no painel Propriedades. 
6. Especifique a consulta do banco de dados para ler os dados de interesse.

```
SELECT [CustomerKey]
      ,[GeographyKey]
      ,[CustomerAlternateKey]
      ,[MaritalStatus]
      ,[Gender]
      ,cast ([YearlyIncome] as int) as SalaryYear
      ,[TotalChildren]
      ,[NumberChildrenAtHome]
      ,[EnglishEducation]
      ,[EnglishOccupation]
      ,[HouseOwnerFlag]
      ,[NumberCarsOwned]
      ,[CommuteDistance]
      ,[Region]
      ,[Age]
      ,[BikeBuyer]
  FROM [dbo].[vTargetMail]
```
7. Execute o teste clicando em **Executar** na tela do teste.
8. Clique na porta de saída na parte inferior do módulo Leitor e selecione **Visualizar** para ver os dados importados.



## Etapa 2: limpar dados
Removeremos algumas colunas que não são relevantes para o modelo.

1. Arraste o módulo **Colunas do Projeto** na tela.
2. Clique em **Iniciar seletor de colunas** no painel Propriedades para especificar quais colunas você deseja remover.
3. Exclua duas colunas: CustomerAlternateKey e GeographyKey.



## Etapa 3: criar modelo
Dividiremos os dados 80-20: 80% para treinar um modelo de aprendizado de máquina e 20% para testar o modelo. Usaremos os algoritmos de “Duas Classes” para esse problema de classificação binária.

1. Arraste o módulo **Divisão** na tela.
2. Insira 0.8 para a Fração de linhas no primeiro conjunto de dados de saída no painel Propriedades.
3. Arraste o módulo **Árvore de Decisão Aumentada de duas classes** na tela.
4. Arraste o módulo **Modelo de Treinamento** na tela e especifique: primeira entrada: algoritmo ML. Segunda entrada: dados para treinar o algoritmo.
5. Clique em **Iniciar seletor de colunas** no painel Propriedades para especificar qual coluna o modelo deve prever: BikeBuyer.


## Etapa 4: pontuar modelo
Agora, testaremos o desempenho do modelo nos dados de teste. Vamos comparar o algoritmo de nossa escolha com um algoritmo diferente para ver que tem um desempenho melhor.

1. Arraste o módulo **Modelo de Pontuação** na tela. Primeira entrada: modelo treinado Segunda entrada: dados de teste
2. Arraste o **Computador de Ponto de Bayes de Duas Classes** na tela de teste. Vamos comparar o desempenho desse algoritmo em comparação com a Árvore de Decisão Aumentada de Duas Classes.
3. Copie e cole os módulos Modelo de Treinamento e Modelo de Pontuação na tela.
4. Arraste o módulo **Avaliar Modelo** na tela para comparar os dois algoritmos.
5. **Execute** o teste.
6. Clique na porta de saída na parte inferior do módulo Avaliar Modelo e clique em Visualizar.


As métricas fornecidas são a curva ROC, o diagrama de comparação de precisão e recolhimento e a curva de comparação de precisão. Ao examinar essas métricas, podemos ver que o primeiro modelo teve um desempenho melhor do que o segundo. Para ver o que o primeiro modelo previu, clique na porta de saída do Modelo de Pontuação e clique em Visualizar.


Você verá duas ou mais colunas adicionadas ao seu conjunto de dados de teste.

- Probabilidades Pontuadas: a probabilidade de que um cliente é um comprador de bicicleta.
- Rótulos Pontuados: a classificação feita pelo modelo – comprador de bicicleta (1) ou não (0). O limite de probabilidade para a rotulagem é definido como 50% e pode ser ajustado.

Ao comparar a coluna BikeBuyer (real) com os Rótulos Pontuados (previsão), é possível ver o desempenho do modelo. Como as próximas etapas, você pode usar esse modelo para fazer previsões para novos clientes e publicar esse modelo como um serviço Web ou gravar os resultados de volta no SQL Data Warehouse.

Para saber mais, veja [Introdução ao Aprendizado de Máquina no Azure][].



<!--Article references-->
[Estúdio de Aprendizado de Máquina do Azure]: https://studio.azureml.net/
[Introdução ao Aprendizado de Máquina no Azure]: ../machine-learning/machine-learning-what-is-machine-learning.md
[carregar dados de exemplo manualmente]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Criar um SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO4-->