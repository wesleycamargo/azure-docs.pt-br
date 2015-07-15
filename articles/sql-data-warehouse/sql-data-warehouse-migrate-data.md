<properties
   pageTitle="Migrar seus dados para o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para migrar seus dados para o SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Migrar seus dados
O principal objetivo ao migrar dados é popular o banco de dados do SQLDW. Esse processo pode ser realizado de várias maneiras. Cópia do ADF, SSIS e bcp podem ser usados para atingir esse objetivo. No entanto, à medida que o volume de dados aumenta, uma boa prática é dividir em etapas o processo de migração de dados. Isso possibilita a otimização de cada etapa quanto ao desempenho e à resiliência para garantir uma migração de dados tranquila.

Em primeiro lugar, este artigo descreve os cenários de migração simples por Cópia do ADF, SSIS e bcp. Em seguida, o artigo se aprofunda um pouco em como a migração pode ser otimizada.

## Cópia do ADF (Azure Data Factory)
A [Cópia do ADF][] faz parte do [Azure Data Factory][]. Você pode usar a Cópia do ADF para exportar dados para arquivos simples que residem no armazenamento local, arquivos simples remotos mantidos no armazenamento de blob do Azure ou diretamente no SQL Data Warehouse.

Se seus dados começam em arquivos simples, você precisará transferi-los para o blob de armazenamento antes de carregá-los no SQL Data Warehouse. Depois que os dados são transferidos para o armazenamento de blob do Azure, você poderá optar por usar a [Cópia do ADF][] novamente para enviar os dados ao SQL Data Warehouse.

O PolyBase também fornece uma opção de desempenho bastante alto para carregamento dos dados. No entanto, isso significa usar duas ferramentas em vez de uma. Se precisar de melhor desempenho, use o PolyBase. Se deseja uma experiência de ferramenta única (e os dados não forem pesados), o ADF é a melhor opção.

> [AZURE.NOTE]O PolyBase exige que os arquivos de dados estejam em UTF-8. Essa é a codificação padrão da Cópia do ADF; sendo assim, nada precisa ser alterado. Este é apenas um lembrete para não mudar o comportamento padrão da Cópia do ADF.

Acesse o seguinte artigo para ver alguns ótimos [exemplos de Cópia do ADF].

## Serviços de integração ##
O SSIS (SQL Server Integration Services) é uma ferramenta de ETL (Extração, Transformação e Carregamento) avançada e flexível que oferece suporte a fluxos de trabalho complexos, transformação de dados e várias opções de carregamento de dados. Use o SSIS para simplificar a transferência de dados para o Azure ou como parte de uma migração mais ampla.

> [AZURE.NOTE]O SSIS pode exportar para UTF-8 sem a marca de ordem de byte no arquivo. Para configurar isso, primeiramente você deve usar o componente de coluna derivada para converter os dados de caractere no fluxo de dados para usar a página de código 65001 UTF-8. Depois que as colunas forem convertidas, grave os dados no adaptador de destino do arquivo simples, garantindo que 65001 também tenha sido selecionado como a página de código para o arquivo.

O SSIS conecta-se ao SQL Data Warehouse da mesma forma que se conecta a uma implantação do SQL Server. No entanto, suas conexões precisarão usar um gerenciador de conexão ADO.NET. Você também deve ter cuidado ao definir a configuração “” para maximizar a taxa de transferência.

> [AZURE.NOTE]Não há suporte para a conexão com o SQL Data Warehouse usando o OLEDB.

Além disso, sempre há a possibilidade de que um pacote possa falhar devido a problemas de limitação ou rede. Crie pacotes que possam ser retomados do ponto de falha, sem precisar refazer o trabalho concluído antes da falha.

Para saber mais, consulte a [documentação do SSIS][].

## bcp
O bcp é um utilitário de linha de comando desenvolvido para importação e exportação de dados de arquivo simples. Há possibilidade de transformação durante a exportação dos dados. Para executar transformações simples, use uma consulta para selecionar e transformar os dados. Depois de exportados, os arquivos simples podem ser carregados diretamente no banco de dados do SQL Data Warehouse de destino.

> [AZURE.NOTE]Uma boa prática recomendada é encapsular as transformações usadas durante a exportação de dados em uma exibição no sistema de origem. Isso garante que a lógica seja mantida e o processo possa ser repetido.

As vantagens do bcp são:

- Simplicidade. A criação e a execução dos comandos do bcp são simples
- Processo de carregamento reinicializável. Depois de exportada, a carga pode ser executada várias vezes

As limitações do bcp são:

- O bcp funciona somente com arquivo simples tabulados. Ele não funciona com arquivos xml ou JSON, por exemplo
- O bcp não oferece suporte à exportação para UTF-8. Isso pode impedir o uso do PolyBase em dados exportados do bcp
- Os recursos de transformação de dados são limitados apenas à fase de exportação e são inerentemente simples
- O bcp não foi adaptado para ser robusto ao carregar dados pela Internet. Qualquer instabilidade de rede pode causar um erro de carregamento.
- O bcp depende do esquema presente no banco de dados de destino antes do carregamento

Para saber mais, consulte [Usar o bcp para carregar dados no SQL Data Warehouse][].

## Otimizando a migração de dados
Um processo de migração de dados SQLDW pode ser dividido efetivamente em três etapas distintas:

1. Exportação dos dados de origem
2. Transferência de dados para o Azure
3. Carregamento no banco de dados do SQLDW de destino

Cada etapa pode ser otimizada individualmente para criar um processo de migração robusto, reinicializável e flexível que maximize o desempenho em cada etapa.

## Otimizando o carregamento de dados
Observando-as na ordem inversa por um momento, a maneira mais rápida de carregar dados é pelo PolyBase. Otimizar um processo de carregamento do PolyBase impõe pré-requisitos nas etapas anteriores, de modo que é melhor entendê-los de antemão. Eles são:

1. Codificação dos arquivos de dados
2. Formatação dos arquivos de dados
3. Localização dos arquivos de dados

### Codificação
O PolyBase exige que os arquivos de dados sejam codificados em UTF-8. Isso significa que, quando você exporta dados, estes devem estar em conformidade com esse requisito. Se seus dados contiverem apenas caracteres ASCII básicos (e não ASCII estendidos), eles serão mapeados diretamente para o padrão UTF-8 e você não precisará se preocupar com a codificação. No entanto, se os dados contiverem caracteres especiais, como diéreses, acentos ou símbolos, ou se oferecerem suporte a idiomas não latinos, você terá que garantir que os arquivos de exportação sejam corretamente codificados em UTF-8.

> [AZURE.NOTE]O bcp não oferece suporte à exportação de dados para UTF-8. Portanto, a melhor opção para a exportação de dados é usar o Integration Services ou a Cópia do ADF. Vale a pena salientar que a BOM (marca de ordem de byte) UTF-8 não é necessária no arquivo de dados.

Todos os arquivos codificados com UTF-16 precisarão ser reescritos ***antes*** da transferência de dados.

### Formatação dos arquivos de dados
O PolyBase exige um terminador de linha fixo de \n ou uma nova linha. Os arquivos de dados devem estar de acordo com esse padrão. Não existem restrições quanto a terminadores de coluna ou de cadeia de caracteres.

Você precisará definir todas as colunas no arquivo como parte de sua tabela externa no PolyBase. Certifique-se de que todas as colunas exportadas sejam necessárias e que os tipos estejam em conformidade com os padrões exigidos.

Consulte novamente o artigo [migrar seu esquema] para obter detalhes sobre os tipos de dados com suporte.

### Localização dos arquivos de dados
O SQL Data Warehouse usa o PolyBase para carregar dados com exclusividade do Armazenamento de Blob do Azure. Consequentemente, os dados devem ser sido transferidos primeiro para o armazenamento de blob.

## Otimizando a transferência de dados
Uma das partes mais lentas da migração de dados é a transferência dos dados para o Azure. Não só a largura de banda da rede pode ser um problema; a confiabilidade também pode atrapalhar seriamente o progresso. Por padrão, a migração de dados para o Azure é pela Internet, de modo que as chances de erros de transferência são razoáveis. No entanto, esses erros podem exigir que os dados sejam reenviados no todo ou em parte.

Felizmente, você tem várias opções para melhorar a velocidade e a resiliência desse processo:

### [Rota Expressa][]
Talvez você queira considerar o uso da [Rota Expressa][] para acelerar a transferência. A [Rota Expressa][] fornece uma conexão privada estabelecida com o Azure para que a conexão não passe pela Internet pública. Essa não é uma etapa obrigatória. No entanto, ela melhora a taxa de transferência ao enviar dados para o Azure de uma instalação local ou colocalizada.

Os benefícios de usar a [Rota Expressa][] são:

1. Maior confiabilidade
2. Rede mais rápida
3. Menor latência da rede
4. Mais segurança de rede

A [Rota Expressa][] é vantajosa em muitos cenários; não apenas na migração.

Interessado? Para saber mais e obter informações sobre preços, acesse a [documentação da Rota Expressa][].

### Serviço de Importação e Exportação do Azure
O Serviço de Importação e Exportação do Azure é um processo de transferência de dados desenvolvido para transferências de volumes de dados grandes (GB++) e em massa (TB++) para o Azure. Ele envolve gravar os dados em discos e enviá-los a um data center do Azure. O conteúdo do disco é carregado no Blob de Armazenamento do Azure em seu nome.

Exibição de alto nível do processo de importação/exportação:

1. Configurar um contêiner de Armazenamento de Blob do Azure para receber os dados
2. Exportar dados para o armazenamento local
2. Copiar dados em unidades de disco rígido SATA II/III de 3,5 polegadas usando a [Ferramenta de Importação/Exportação do Azure].
3. Criar um Trabalho de Importação usando o Serviço de Importação e Exportação do Azure fornecendo os arquivos de diário gerados pela [Ferramenta de Importação/Exportação do Azure].
4. Enviar os discos para o data center do Azure indicado
5. Seus dados são transferidos para o contêiner de Armazenamento de Blob do Azure
6. Carregar os dados no SQLDW usando o PolyBase

### Utilitário [AZCopy][]
O utilitário [AZCopy][] é uma excelente ferramenta para transferir dados para os Blobs de Armazenamento do Azure. Ele foi desenvolvido para transferências de volumes de dados pequenos (MB++) a muito grandes (GB++). O [AZCopy] também foi projetado para fornecer boa taxa de transferência resiliente ao transferir dados para o Azure e, portanto, é uma excelente opção para a etapa de transferência de dados. Depois de transferidos, você pode carregar os dados no SQL Data Warehouse usando o PolyBase. Também é possível incorporar o AZCopy aos seus pacotes do SSIS usando uma tarefa “Executar Processo”.

Para usar o AZCopy, primeiramente é preciso baixá-lo e instalá-lo. Estão disponíveis uma [versão de produção][] e uma [versão de visualização][].

Para carregar um arquivo do seu sistema de arquivos, será necessário um comando como o mostrado abaixo:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Exemplo do resumo de um processo de alto nível:

1. Configurar um contêiner de blob de armazenamento do Azure para receber os dados
2. Exportar dados para o armazenamento local
3. Copiar os dados no contêiner de Armazenamento de Blob do Azure
4. Carregar os dados no SQL Data Warehouse usando PolyBase

Documentação completa disponível: [AZCopy][].

## Otimizando a exportação de dados
Além de assegurar que a exportação está em conformidade com os requisitos apresentados pelo PolyBase, você também pode otimizar a exportação dos dados para melhorar ainda mais o processo.

> [AZURE.NOTE]Como o PolyBase exige que os dados estejam no formato UTF-8, é improvável que você use o bcp para executar a exportação de dados. O bcp não oferece suporte à saída de arquivos de dados em UTF-8. O SSIS ou a Cópia do ADF são muito mais adequados para realizar esse tipo de exportação de dados.

### Compactação de dados
O PolyBase pode ler dados compactados em gzip. Se for possível compactar os dados em arquivos gzip, você minimizará o volume de dados que é enviado pela rede.

### Vários arquivos
Dividir tabelas grandes em vários arquivos ajuda a aumentar a velocidade da exportação, bem como ajuda na capacidade de reinicialização da transferência e na capacidade de gerenciamento geral dos dados quando eles estiverem no armazenamento de blob do Azure. Um dos muitos recursos interessantes do PolyBase é que ele lê todos os arquivos dentro de uma pasta e os trata como uma tabela. Desse modo, é uma boa ideia isolar os arquivos de cada tabela em sua própria pasta.

O PolyBase também oferece suporte a um recurso conhecido como "passagem de pasta recursiva". Você pode usar esse recurso para aprimorar ainda mais a organização dos seus dados exportados, melhorando o gerenciamento de dados.

Para saber mais sobre o carregamento de dados com o PolyBase, consulte [Usar o PolyBase para carregar dados no SQL Data Warehouse][].


## Próximas etapas
Para saber mais sobre a migração, consulte [Migrar sua solução para o SQL Data Warehouse][]. Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[AZCopy]: storage/storage-use-azcopy.md
[Cópia do ADF]: storage/data-factory-copy-activity.md
[exemplos de Cópia do ADF]: data-factory/data-factory-copy-activity-examples.md
[Visão geral de desenvolvimento]: sql-data-warehouse-develop-overview.md
[Migrar sua solução para o SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Usar o bcp para carregar dados no SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Usar o PolyBase para carregar dados no SQL Data Warehouse]: sql-data-warehouse-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[Rota Expressa]: http://azure.microsoft.com/services/expressroute/
[documentação da Rota Expressa]: http://azure.microsoft.com/documentation/services/expressroute/

[versão de produção]: http://aka.ms/downloadazcopy/
[versão de visualização]: http://aka.ms/downloadazcopypr/
[documentação do SSIS]: https://msdn.microsoft.com/pt-br/library/ms141026.aspx

<!---HONumber=July15_HO1-->