<properties title="Use the sample datasets in Azure Machine Learning Studio" pageTitle="Use the sample datasets in Machine Learning Studio | Azure" description="Use the sample datasets in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Usar os conjuntos de dados de amostra no Estúdio de Aprendizado de Máquina do Azure

O Estúdio AM inclui alguns conjuntos de dados de amostra para você usar. Esses são conjuntos de dados de aprendizado de máquina padrão de domínio público.

Você pode usar esses conjuntos de dados em seus testes enquanto explora vários tipos de modelos de aprendizado de máquina analíticos no Estúdio AM.

-   Para exibir uma lista desses conjuntos de dados padrão no Estúdio AM, clique na guia **CONJUNTOS DE DADOS**. Para cada conjunto de dados, você pode ver informações como o nome do conjunto de dados, quem o enviou e uma breve descrição.

    Para classificar os conjuntos de dados, clique em qualquer cabeçalho da coluna. Por exemplo, clique em **ENVIADO** para agrupar todos os conjuntos de dados de amostra fornecidos pela Microsoft Corporation. Essa também é uma maneira fácil de visualizar conjuntos de dados que você e outros usuários em seu espaço de trabalho importaram.

-   Para usar um conjunto de dados em um teste, expanda a seção **Conjunto de dados salvos** da paleta do módulo à esquerda da tela de teste, ou pesquise por um conjunto de dados específico digitando o nome na caixa de pesquisa acima da paleta. Arraste o conjunto de dados na tela para adicioná-lo ao seu teste.

## Conjuntos de dados de amostra

A tabela a seguir fornece informações adicionais sobre os conjuntos de dados que podem ser úteis a você enquanto explora os recursos de aprendizado de máquina do Estúdio AM. Para cada conjunto de dados, a tabela lista:

-   O nome e a origem de cada conjunto de dados.
-   Uma descrição dos possíveis usos do conjunto de dados e das citações para pesquisa de aprendizado de máquina que utilizaram o conjunto de dados.
-   Um resumo das colunas mais importantes está incluído no conjunto de dados e outros atributos úteis.

Algumas descrições são adaptadas da documentação fornecida pela fonte (normalmente [UC Irvine Machine Learning Repository][UC Irvine Machine Learning Repository]), enquanto outras descrições têm por base uma investigação adicional ou refletem mudanças feitas nessas amostras.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Nome do conjunto de dados</th>
<th align="left">Descrição do conjunto de dados</th>
<th align="left">Pesquisa relacionada</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Conjunto de dados de classificação binária de receita no recenseamento adulto</td>
<td align="left"><p>Um subconjunto do banco de dados do recenseamento de 1994, usando adultos em fase de trabalho, com idade acima de 16 anos com um índice de receita ajustado de &gt; 100.</p>
<p><strong>Uso:</strong> Classificar pessoas usando dados demográficos para prever se uma pessoa recebe acima de 50 mil por ano.</p></td>
<td align="left"><p>Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="even">
<td align="left"><p>Dados de preço de automóvel (Brutos)</p></td>
<td align="left"><p>Informações sobre automóveis por marca e modelo, incluindo o preço, recursos como número de cilindradas e MPG, bem como uma pontuação de risco de seguro.</p>
<p>A pontuação de risco é inicialmente associada ao preço do automóvel e depois é ajustada quanto ao risco real em um processo conhecido pelos atuários como <legacyitalic>valor simbólico</legacyitalic>. Um valor de +3 indica que o automóvel apresenta risco e um valor de -3 indica que ele provavelmente é bem seguro.</p>
<p><strong>Uso:</strong> Prever a pontuação de risco por recursos, usando regressão ou classificação de multivariável.</p></td>
<td align="left"><p>Schlimmer, J.C. (1987). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Dados de doação de sangue</p></td>
<td align="left"><p>Um subconjunto de dados do banco de dados de doadores de sangue do Centro de Serviços de Transfusão de Sangue da Cidade de Hsin-Chu, Taiwan.</p>
<p>Os dados do doador incluem os meses desde a última doação e a frequência, ou o número total de doações, data da última doação e a quantidade de sangue doado.</p>
<p><strong>Uso:</strong> O objetivo é prever, por meio de classificação, se o doador doou sangue em março de 2007, onde 1 indica um doador durante o período alvo, 0 nenhum doador.</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Yeh, I.C., (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
</listitem> <listitem>
<p>Yeh, I-Cheng, Yang, King-Jang, and Ting, Tao-Ming, &quot;Knowledge discovery on RFM model using Bernoulli sequence, &quot;Expert Systems with Applications, 2008, [<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018" class="uri">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>Dados de câncer de mama</p></td>
<td align="left"><p>Um dos três conjuntos de dados relacionados a câncer fornecidos pelo Instituto de Oncologia que aparece frequentemente na literatura de aprendizado de máquina. Ele combina informações de diagnóstico com recursos de análise de laboratório de aproximadamente 300 amostras de tecido.</p>
<p><strong>Uso:</strong> Classificar o tipo de câncer, com base em 9 atributos, alguns dos quais são lineares e alguns são categóricos.</p></td>
<td align="left"><p>Wohlberg, W.H., Street, W.N., &amp; Mangasarian, O.L. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Dados de regressão de eficiência de energia</p></td>
<td align="left"><p>Uma coleção de perfis de energia simulados, com base em 12 formatos de construções diferentes. As construções diferem com relação a 8 recursos, como área envidraçada, distribuição da área envidraçada e orientação.</p>
<p><strong>Uso:</strong> Usar a regressão ou a classificação para prever a taxa de eficiência energética com base em uma das duas respostas de valor real. Para classificação de multiclasse, a variável da resposta é arredondada para o número inteiro mais próximo.</p></td>
<td align="left"><p>Xifara, A. &amp; Tsanas, A. (2012). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="even">
<td align="left"><p>Desempenho pontual de voo (Bruto)</p></td>
<td align="left"><p>Registros de pousos e decolagens nos Estados Unidos desde outubro de 2011.</p>
<p><strong>Uso:</strong> Prever atrasos nos voos.</p></td>
<td align="left"><p>From US Dept. of Transportation [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time" class="uri">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Dados de incêndios florestais</p></td>
<td align="left"><p>Contém dados meteorológicos, como índices de temperatura e umidade e velocidade do vento, de uma área no nordeste de Portugal, combinados com registros de incêndios florestais.</p>
<p><strong>Uso:</strong> Essa é uma tarefa de regressão difícil, em que o objetivo é prever a área queimada nos incêndios florestais.</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Cortez, P., &amp; Morais, A. (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
</listitem> <listitem>
<p>[Cortez and Morais, 2007] P. Cortez and A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. In J. Neves, M. F. Santos and J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, December, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponível em: [<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf" class="uri">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>Dados da íris classe dois</p></td>
<td align="left"><p>Esse é provavelmente o banco de dados mais conhecido encontrado na literatura de reconhecimento padrão. O conjunto de dados é relativamente pequeno, contém 50 exemplos de cada medida em pétalas das três variáveis da íris.</p>
<p><strong>Uso:</strong> Prever o tipo de íris a partir das medições. <!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now. One class is linearly separable from the other two; but the latter are not linearly separable from each other. --></p></td>
<td align="left"><p>Fisher, R.A. (1988). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Dados MPG para vários automóveis</p></td>
<td align="left"><p>Esse conjunto de dados é uma versão um pouco modificada do conjunto de dados fornecido pela Biblioteca StatLib da Universidade de Carnegie Mellon. O conjunto de dados foi usado na exposição da Associação Estatística Americana de 1983.</p>
<p>Os dados listam o consumo de combustível de vários automóveis em milhas por galão, juntamente com informações como o número de cilindros, cilindradas do motor, cavalos de potência, peso total e aceleração.</p>
<p><strong>Uso:</strong> Prever a economia de combustível com base em 3 atributos discretos de multivalores e 5 atributos contínuos.</p></td>
<td align="left"><p>StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="even">
<td align="left"><p>Conjunto de dados de classificação binária de diabetes da população indiana de Pima</p></td>
<td align="left"><p>Um subconjunto de dados do Instituto Nacional de Diabetes e o banco de dados de Doenças Digestivas e Renais. O conjunto de dados foi filtrado para se concentrar nas pacientes mulheres com hereditariedade indiana de Pima. Os dados incluem registros médicos como níveis de glicose e insulina, bem como fatores do estilo de vida.</p>
<p><strong>Uso:</strong> Prever se o indivíduo tem diabetes (classificação binária).</p></td>
<td align="left"><p>Sigillito, V. (1990). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Dados de consumidores de restaurantes</p></td>
<td align="left"><p>Um conjunto de metadados sobre consumidores, incluindo demografia e preferências.</p>
<p><strong>Uso:</strong> Usar esse conjunto de dados, em combinação com outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação.</p></td>
<td align="left"><p>Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Dados de recurso de restaurante</p></td>
<td align="left"><p>Um conjunto de metadados sobre restaurantes e seus recursos, como tipo de comida, estilo de jantar e localização.</p>
<p><strong>Uso:</strong> Usar esse conjunto de dados, em combinação com outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação.</p></td>
<td align="left"><p>Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Classificação de restaurantes</p></td>
<td align="left"><p>Contém a classificação fornecida por usuários de restaurantes em uma escala de 0 a 2.</p>
<p><strong>Uso:</strong> Usar esse conjunto de dados, em combinação com outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação.</p></td>
<td align="left"><p>Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Conjunto de dados multiclasses de recozimento de aço</p></td>
<td align="left"><p>Esse conjunto de dados contém uma série de registros das avaliações de recozimento de aço com atributos físicos (largura, espessura, tipo (bobina, chapa, etc.) dos tipos de aço resultantes.</p>
<p><strong>Uso:</strong> Prever um dos dois atributos de classe numérica, resistência ou dureza. Você também pode analisar correlações entre os atributos.</p>
<p>Os graus de aço seguem um padrão definido pela SAE e outras organizações. Você está procurando um “grau” específico (a variável de classe) e deseja entender os valores necessários.</p></td>
<td align="left"><p>Sterling, D. &amp; Buntine, W., (NA). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
<p>Um guia útil para graus de aço:</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf&gt;]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Dados de telescópio</p></td>
<td align="left"><p>Registros de explosões de partículas gama de alta energia com ruídos de fundo, ambos simulados usando o processo de Monte Carlo.</p>
<p>O objetivo da simulação era melhorar a precisão dos telescópios gama de Cherenkov atmosféricos e em terra, usando métodos estatísticos para diferenciar entre o sinal desejado (expositores de radiação de Cherenkov) e o ruído de fundo (expositores hadrônicos iniciados por raios cósmicos na atmosfera superior).</p>
<p>Os dados foram pré-processados para criar um conjunto elíptico com o eixo longo direcionado para o centro da câmera. As características dessa elipse, (geralmente chamada de parâmetros de Hillas) estão entre os parâmetros da imagem que podem ser usados para discriminação.</p>
<p><strong>Uso:</strong> Prever se a imagem de um expositor representa sinal ou ruído de fundo.</p>
<p><strong>Observações:</strong> A precisão da simples classificação não é significativa para esses dados, já que classificar evento de fundo como sinal é pior do que classificar um evento de sinal como sendo de fundo. Para obter uma comparação dos diferentes classificadores, o gráfico ROC deve ser usado. A probabilidade de aceitar um evento de fundo como sinal deve estar abaixo de um dos seguinte limites: 0,01 , 0,02 , 0,05 , 0,1 ou 0,2.</p>
<p>Também, observe que o número de eventos de fundo (h, para expositores hadrônicos) é subestimado, ao passo que nas medições reais, o h ou a classe de ruído representa a maioria dos eventos.</p></td>
<td align="left"><p>Bock, R.K. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p></td>
</tr>
</tbody>
</table>

Os tipos de conjuntos de dados que você pode encontrar estão listados abaixo.

| Formato de dados                                 | Descrição                                                                                                                                                                                                                           |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Valores separados por vírgulas (CSV)             | Formato de intercâmbio bem-conhecido para todas as plataformas de dados. O Estúdio AM carrega esses dados e incorpora um adivinhador de tipo em todas as colunas que aplica metadados com base nos dados amostrados de cada coluna. |
| Formato de arquivo de relação de atributo (ARFF) | Formato de dados de Aprendizado de Máquina definido por WEKA. Contém metadados (para tipos nominal, numérico e cadeia de caracteres) que os convertem diretamente em tabelas de dados do Estúdio MA.                                |
| Texto sem formatação                             | O texto sem formatação pode ser lido e depois dividido em colunas com a ajuda de módulos de pré-processamento de downstream. A maior parte do formato de texto sem formatação suportada é JSON.                                     |
| DotNetTable                                      | A versão em série do contêiner primário transmitido entre módulos no Estúdio AM (o *conjunto de dados*).                                                                                                                            |

  [UC Irvine Machine Learning Repository]: http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository"
