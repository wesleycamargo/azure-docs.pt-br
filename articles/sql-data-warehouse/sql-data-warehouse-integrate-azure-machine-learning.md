<properties
   pageTitle="Use o Aprendizado de Máquina do Azure com o SQL Data Warehouse | Microsoft Azure"
   description="Tutorial para usar o Aprendizado de Máquina do Azure com o Data Warehouse do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="shivaniguptamsft"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/17/2016"
   ms.author="shigu;barbkess;sonyama"/>

# Use o Aprendizado de Máquina do Azure com o SQL Data Warehouse

O Aprendizado de Máquina do Azure é um serviço de análise preditiva totalmente gerenciado que você pode usar para criar modelos preditivos em relação aos dados do SQL Data Warehouse e publicá-los como serviços Web prontos para consumo. Você pode aprender os fundamentos da análise de previsão e aprendizado de máquina lendo [Introdução ao Aprendizado de Máquina no Azure][]. Em seguida, você pode aprender como criar, treinar, pontuar e testar um modelo de aprendizado de máquina usando o [Criar tutorial de teste][].

Neste artigo, você aprenderá a fazer o seguinte usando o [Estúdio de Aprendizado de Máquina do Azure][]\:

- Ler dados de seu banco de dados para criar, treinar e pontuar um modelo de previsão
- Gravar dados em seu banco de dados


## Exportar dados do SQL Data Warehouse

Leremos dados da tabela Produtos no banco de dados AdventureWorksDW.

### Etapa 1

Inicie um novo experimento clicando em +NOVO na parte inferior da janela do Estúdio de Aprendizado de Máquina, selecione EXPERIMENTO e selecione Experimento em branco. Selecione o nome de experimento padrão na parte superior da tela e renomeie para algo significativo, por exemplo, Previsão de preço de bicicleta.

### Etapa 2

Procure o módulo Leitor na paleta de conjuntos de dados e módulos à esquerda da tela do experimento. Arraste o módulo à tela do experimento. ![][drag_reader]

### Etapa 3

Selecione o módulo Leitor e preencha o painel de propriedades.

1. Selecione o Banco de Dados SQL do Azure como a Fonte de dados.
2. Nome do servidor de banco de dados: digite o nome do servidor. Você pode usar o [portal do Azure][] para encontrar isso.

![][server_name]

3. Nome do banco de dados: digite o nome do banco de dados no servidor especificado.
4. Nome de conta de usuário do servidor: digite o nome de usuário de uma conta que tenha permissões de acesso para o banco de dados.
5. Senha de conta de usuário do servidor: forneça a senha da conta de usuário especificada.
6. Aceitar qualquer certificado do servidor: use essa opção (menos segura) se desejar ignorar a revisão do certificado do site antes de ler os dados.
7. Consulta de banco de dados: insira uma instrução SQL que descreve os dados que você deseja ler. Nesse caso, vamos ler dados da tabela de produto usando a consulta a seguir.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### Etapa 4

1. Execute o experimento clicando em Executar abaixo da tela do experimento.
2. Quando o teste for concluído, o módulo Leitor terá uma marca de seleção verde para indicar que foi concluída com êxito. Observe também o status Execução concluída no canto superior direito.

![][run]

3. Para ver os dados importados, clique na porta de saída na parte inferior do conjunto de dados automóvel e selecione Visualizar.


## Crie, treine e pontue um modelo

Agora você pode usar esse conjunto de dados para:

- Criar um modelo: processar dados e definir recursos
- Treinar o modelo: escolher e aplicar um algoritmo de aprendizado
- Pontuar e testar o modelo: prever o novo preço de uma bicicleta


![][model]

Para saber mais sobre como criar, treinar, pontuar e testar uma modelo de aprendizado de máquina, use o [Tutorial de criação de experimento][].

## Grave dados no SQL Data Warehouse do Azure

Gravaremos o conjunto de resultados na tabela ProductPriceForecast no banco de dados AdventureWorksDW.

### Etapa 1

Procure o módulo Gravador na paleta de conjuntos de dados e módulos à esquerda da tela do experimento. Arraste o módulo à tela do experimento.

![][drag_writer]

### Etapa 2

Selecione o módulo Gravador e preencha o painel de propriedades.

1. Selecione o Banco de Dados do SQL Azure como o Destino de Dados.
2. Nome do servidor de banco de dados: digite o nome do servidor. Você pode usar o [portal do Azure][] para encontrar isso.
3. Nome do banco de dados: digite o nome do banco de dados no servidor especificado.
4. Nome de conta de usuário do servidor: digite o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
5. Senha de conta de usuário do servidor: forneça a senha da conta de usuário especificada.
6. Aceitar qualquer certificado de servidor (não é seguro): selecione esta opção se você não deseja exibir o certificado.
7. Lista separada por vírgulas de colunas a serem salvas: forneça uma lista das colunas de conjunto de dados ou o resultado de saída.
8. Nome da tabela de dados: especifique o nome da tabela de dados.
9. Lista separada por vírgulas de colunas da datatable: especifique os nomes de coluna a serem usados na nova tabela. Os nomes de coluna podem ser diferentes do conjunto de dados de origem, mas você deve listar o mesmo número de colunas aqui que você definir para a tabela de saída.
10. Número de linhas gravadas por operação do SQL Azure: você pode configurar o número de linhas que são gravados em um banco de dados SQL em uma única operação.

![][writer_properties]

### Etapa 3

1. Execute o experimento clicando em Executar abaixo da tela do experimento.
2. Quando o experimento for concluído, todos os módulos terão uma marca de seleção verde para indicar que foram concluídos com sucesso.

## Próximas etapas

Para obter uma visão geral da integração, consulte [Visão geral de integração do SQL Data Warehouse][].

Para obter mais dicas de desenvolvimento, confira [Visão geral sobre o desenvolvimento no SQL Data Warehouse][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Visão geral sobre o desenvolvimento no SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Visão geral de integração do SQL Data Warehouse]: ./sql-data-warehouse-overview-integration.md
[Criar tutorial de teste]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Tutorial de criação de experimento]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introdução ao Aprendizado de Máquina no Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Estúdio de Aprendizado de Máquina do Azure]: https://studio.azureml.net/Home
[portal do Azure]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

<!---HONumber=AcomDC_0518_2016-->