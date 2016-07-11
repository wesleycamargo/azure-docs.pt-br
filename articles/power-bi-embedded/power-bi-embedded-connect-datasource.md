<properties
   pageTitle="Microsoft Power BI Embedded - Conectando-se a uma fonte de dados"
   description="Power BI Embedded, conectar-se a fontes de dados"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/28/2016"
   ms.author="owend"/>

# Conectar-se a uma fonte de dados

Com o **Power BI Embedded**, você pode inserir relatórios em seu próprio aplicativo. Quando você insere um relatório do Power BI em seu aplicativo, o relatório conecta-se aos dados subjacentes por meio da **importação** de uma cópia dos dados ou **conectando-se diretamente** à fonte de dados usando o **DirectQuery**.

Aqui estão as diferenças entre o uso de **Importar** e **DirectQuery**.

|Importar | DirectQuery
|---|---
|Tabelas, colunas, *e dados* são importados ou copiados para o conjunto de dados do relatório. Para ver as alterações ocorridas nos dados subjacentes, você deve atualizar ou importar novamente um conjunto de dados completo e atual.|Somente *tabelas e colunas* são importados ou copiados para o conjunto de dados do relatório. Você sempre pode exibir os dados mais atuais.

A próxima seção descreve os benefícios e limitações do uso do **DirectQuery**.

## Benefícios do uso do DirectQuery

Há duas vantagens principais ao usar **DirectQuery**:

   -	O **DirectQuery** permite compilar visualizações em conjuntos de dados muito grandes nos quais, caso contrário, seria inviável importar primeiro todos os dados.
   -	Alterações de dados subjacentes podem exigir uma atualização de dados e, para alguns relatórios, a necessidade de exibir dados atuais pode exigir grandes transferências de dados, tornando a importação de dados novamente inviável. Por outro lado, relatórios do **DirectQuery** sempre usam dados atuais.

## Limitações do DirectQuery

   Há algumas limitações no uso do **DirectQuery**:

   -	Todas as tabelas devem vir de um banco de dados individual.
   -	Se a consulta for excessivamente complexa, ocorrerá um erro. Para corrigir o erro, você deve refatorar a consulta para que se torne menos complexa. Se for necessário que a consulta seja complexa, você precisará importar os dados em vez de usar o **DirectQuery**.
   -	Filtragem de relação é limitada a uma única direção, em vez de ambos os trajetos.
   -	Você não pode alterar o tipo de dados de uma coluna.
   -	Por padrão, as limitações são colocadas em expressões DAX permitidas em medidas. Consulte [DirectQuery e medidas](#measures).

<a name="measures"/>
## DirectQuery e medidas

Para garantir que as consultas enviadas à fonte de dados subjacente tenham um desempenho aceitável, limitações são impostas para as medidas. Ao usar o **Power BI Desktop**, usuários avançados podem optar por ignorar essa limitação, escolhendo **Arquivo > Opções e configurações > Opções**. Na caixa de diálogo **Opções**, escolha **DirectQuery** e selecione a opção **Permitir medidas irrestritas no modo DirectQuery**. Quando essa opção estiver selecionada, qualquer expressão DAX válida para uma determinada medida poderá ser usada. No entanto, os usuários devem estar cientes de algumas expressões que funcionam muito bem quando os dados são importados podem resultar em consultas muito lentas para a origem de back-end no modo **DirectQuery**. Para saber mais sobre como usar o **Power BI Desktop**, consulte [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

## Consulte também
- [Introdução ao Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)

<!---HONumber=AcomDC_0629_2016-->