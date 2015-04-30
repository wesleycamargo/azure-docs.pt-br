<properties 
	pageTitle="Opções para transferir dados armazenados para a nuvem | Azure" 
	description="Diretrizes para escolher a melhor opção para transferir dados de locais que não fontes de nuvem para o Microsoft Azure para análise avançada." 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="1/7/2014" 
	ms.author="cgronlun"/>

# Opções para transferir dados armazenados na nuvem do Azure

Este artigo fornece diretrizes para escolher a opção certa para transferir dados de implantação local ou outras fontes de nuvem para o Microsoft Azure para análise de dados avançada. Transferir grandes quantidades de dados pode levar um longo tempo e exige a segurança adequada. 

Neste artigo:

* [Serviço de Importação/Exportação do Azure para armazenamento de Blob](#blob)
* [Utilitário AZCopy](#azcopy-utility)
* [PowerShell do Azure](#ps)
* [Fábrica de dados do Azure (visualização)](#data-factory)
* [Ferramentas de migração do Banco de Dados SQL do Azure](#tools)
* [Sincronização de Dados do SQL Azure (visualização)](#data-sync)
* [Hubs de eventos do Azure](#event-hubs)
* [Outras opções para transferência de dados](#other)
* [Escolha a opção de transferência de dados correta](#choose)


## Serviço de Importação/Exportação do Azure para armazenamento de Blob

Você pode usar o serviço de Importação/Exportação do Azure para transferir grandes quantidades de dados de arquivo para o armazenamento de Blob do Azure em situações em que o carregamento pela rede seja extremamente dispendioso ou inviável. Grandes conjuntos de dados demoram muito tempo para serem carregados ou baixados pela rede. Por exemplo, 10 TB levam 1 mês por T3 (44,7 Mbps). Com o serviço de Importação/Exportação do Microsoft Azure, os clientes podem enviar o disco rígido para reduzir o tempo para carregar ou baixar dados. Planeje vários dias, incluindo o envio. 

Para transferir um grande conjunto de dados de arquivo para o armazenamento de Blob, você pode enviar um ou mais discos rígidos contendo esses dados para um data center do Azure, onde seus dados serão carregados em sua conta de armazenamento. Da mesma forma, para exportar dados do armazenamento de Blob, você pode enviar discos rígidos vazios para um data center do Azure, onde os dados do Blob de sua conta de armazenamento serão copiados para os discos rígidos e, em seguida, devolvidos para você. Antes de enviar uma unidade contendo dados, você criptografará os dados na unidade; quando o serviço de Importação/Exportação exportar seus dados para enviar para você, os dados também serão criptografados antes do envio.

Para obter mais informações, consulte [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados ao armazenamento de Blob][importar-exportar].


## Utilitário AZCopy

AzCopy é um utilitário de linha de comando projetado para upload, download e cópia de dados de alto desempenho do e para o armazenamento de blob, arquivo e tabela do Microsoft Azure. Esse utilitário é adequado para uma única movimentação de dados entre o armazenamento do Azure e o local se a transferência de dados pela rede for prática. Consulte [Introdução ao utilitário de linha de comando do AzCopy][azcopy].

> [AZURE.NOTE] Usuários do Linux: [Baixem o ACP, AzCopy para Linux](http://www.paratools.com/acp)


## PowerShell do Azure

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Você pode usar o PowerShell do Azure para carregar dados no armazenamento de Blob para que os dados possam ser processados por trabalhos MapReduce.

Consulte também:

* [Carregar dados para o armazenamento de Blob usando o PowerShell do Azure][carregar]
* [Como instalar e configurar o PowerShell do Azure][instalar]


## Fábrica de dados do Azure (visualização)

A Fábrica de dados do Azure é um serviço completamente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificados, escalonáveis e confiáveis.

Os desenvolvedores podem compilar fluxos de trabalho orientados por dados que adicionam, agregam e transformam dados estruturados, semiestruturados e não estruturados obtidos dos serviços locais (por meio do Gateway de gerenciamento de dados), baseados em nuvem e de Internet e configurar processamento de dados complexo por meio de scripts JSON simples. Os dados resultantes podem ser armazenados no Armazenamento do Azure ou no Banco de Dados SQL do Azure para análises avançadas.

Especificamente, um desenvolvedor pode coordenar atividades regulares de cópia entre várias origens e destinos mostrados na seção "[Origens e coletores com suporte](data-factory-copy-activity.md#SupportedSourcesAndSinks)" de [Copiar dados com a Fábrica de dados do Azure](data-factory-copy-activity.md), que também inclui propriedades para diferentes tipos de armazenamento de dados, mapeamento de coluna, formatos de serialização e manipulação de tipo.

O serviço pode tratar falhas de reinício automático e permitir a conversão de formato ao mover dados de um formato para outro. Para definir uma atividade de cópia, consulte [Introdução à fábrica de dados][iniciar]. As experiências do registro do armazenamento de dados e da instalação do gateway são descritas em [Habilitar pipelines para trabalhar com dados locais][pipelines]. 

Consulte também:

* [Introdução à Fábrica de dados][introdução]

## Ferramentas de migração do Banco de Dados SQL do Azure

Há muitas ferramentas disponíveis para migrar com sucesso bancos de dados locais do SQL Server e não do SQL Server e para o Banco de Dados SQL do Azure. A melhor ferramenta para seu cenário depende do tipo, do tamanho e da complexidade do banco de dados que está sendo migrado:

* Você pode migrar o esquema e os dados de um Banco de Dados SQL do Azure existente, exportar o banco de dados, armazenar o arquivo de exportação em uma conta de armazenamento de Blob do Azure e importar como um Banco de Dados SQL do Azure novo. O arquivo criado com essa exportação é conhecido como arquivo BACPAC. Para obter mais informações, consulte [Como: Use o serviço de importação e exportação no Banco de Dados SQL do Azure][sql-importar].
* O recurso de cópia do Banco de Dados SQL cria um novo banco de dados no Azure é uma cópia consistente transacional de um Banco de Dados SQL do Azure existente. Para obter mais informações, consulte [Copiando Banco de Dados SQL do Azure][sql-copiar].
* Os Serviços de integração do SQL Server (SSIS) podem ser usados quando são necessárias transformações complexas de dados. O SSIS pode ser usado para mover dados dentro e fora do Banco de Dados SQL do Azure. Para obter mais informações, consulte [Como: Use os serviços de integração para migrar um banco de dados para o Banco de Dados SQL do Azure][integrar] e [SSIS para Azure e movimentação de dados híbridos][SSIS].
* O Assistente para importação e exportação do SQL Server é uma maneira fácil de criar um pacote do SSIS para migrar os dados. Depois de configurar a origem e o destino, você pode especificar transformações de dados básicas. Esses pacotes podem ser salvos, modificados, executados e agendados como um trabalho. Para obter mais informações, consulte [Como: Use o Assistente de importação e exportação para migrar um banco de dados para o Banco de Dados SQL do Azure][assistente].
* O SQL Migration Wizard é uma ferramenta que ajuda a migrar o esquema e os dados entre o SQL Server local e o Banco de Dados SQL do Azure, bem como entre os servidores de Banco de Dados SQL. A ferramenta também analisa os arquivos de rastreamento e scripts quanto a problemas de compatibilidade com o Banco de Dados SQL do Azure. Para obter mais informações, consulte [Como: Use o SQL Database Migration Wizard][usar-assistente].
* O utilitário bcp pode ser usado para importar grandes números de novas linhas para tabelas do SQL Server ou para exportar dados de tabelas para arquivos de dados. Para obter mais informações, consulte [Como: Usar o bcp para migrar um banco de dados para o Banco de Dados SQL do Azure][bcp].

Consulte também:

* [Migrando bancos de dados para o Banco de Dados SQL do Azure][migrar]
 

## Sincronização de Dados do SQL Azure (visualização)

Sincronização de Dados do SQL (visualização) permite criar e agendar sincronizações regulares entre o Banco de Dados SQL do Azure e bancos de dados hospedados no SQL Server ou Banco de Dados SQL do Azure.

A Sincronização de Dados do SQL é adequada para os desenvolvedores de dados sincronizarem as alterações delta entre bancos de dados do Azure locais e na nuvem. Consulte [Sincronização de Dados do SQL][sincronizar].

## Hubs de eventos do Azure

Os hubs de eventos do Microsoft Azure é um serviço de ingestor de evento que fornece entrada de eventos e telemetria para a nuvem em grande escala, com baixa latência e alta confiabilidade. Esse serviço, usado com outros serviços downstream, é especialmente útil em cenários da Internet das coisas, processamento de fluxo de trabalho ou experiência do usuário e instrumentação do aplicativo.

Os desenvolvedores podem escrever código para enviar dados para o hub de eventos, processar os dados e armazenar no Blob do Azure ou no Banco de Dados SQL do Azure para processar posteriormente.

Como alternativa, os desenvolvedores podem aproveitar a análise de fluxo do Azure, um serviço completamente gerenciado que fornece processamento de eventos complexo dimensionável sobre os dados de streaming para saída. O desenvolvedor pode escolher um fluxo do Hub de eventos do Azure, especificar o formato que está sendo usado para serializar o evento de entrada (por exemplo, formatos JSON, CSV ou Avro) e, em seguida, adicione o local de saída, incluindo o Blob do Azure ou o Banco de Dados SQL do Azure.

Consulte:

* [Informações do serviço dos hubs de evento](/services/event-hubs/)
* [Visão geral de Hubs de evento][visão geral]
* [Introdução ao Stream Analytics do Azure][fluxo]

## Outras opções para transferência de dados

As Conexões Híbridas oferecem um modo fácil e prático de conectar sites do Azure e Serviços Móveis do Azure a recursos locais. Os desenvolvedores podem compilar um site para mover dados do local para o Azure. Consulte [Visão geral de conexões híbridas][híbrido] para obter detalhes.

Com a [Rede Virtual](/services/virtual-network/), você pode usar as ferramentas de integração de dados em execução na máquina virtual do Azure para se conectar com segurança a Bancos de Dados SQL Server local no datacenter local. Somente máquinas virtuais e serviços dentro da mesma rede virtual podem identificar ou se conectar entre si. Se preferir, você pode até mesmo criar uma [ExpressRoute](/services/expressroute/) conexão direta ao Azure por meio de seu provedor de serviços de rede ou do provedor de troca e desviar da Internet pública ao mesmo tempo.

O [Azure Marketplace](?source=datamarket.md) oferece soluções de parceiros que habilitam a movimentação de dados no Azure, por exemplo, transferência de arquivos gerenciada Storm.

## Escolha a opção de transferência de dados correta

### Árvore de decisão

![Ajuda a decidir qual opção de transferência de dados em nuvem escolher.][decisão]

Observações sobre a árvore de decisão:

* A Sincronização de Dados do SQL (visualização) aciona uma sincronização de dados alterados.
* A Fábrica de dados (visualização) dá suporte para copiar dados entre o Armazenamento do Azure, o Banco de Dados SQL do Azure e o SQL Server local. 
* Além de usar a Fábrica de dados, você pode estender pacotes existentes do SSIS para mover dados para a nuvem de maneira programada.

### Mover GBs de dados

<table border="1">
<tr>
<th>Mover dados</th>
<th>Uma vez</th>
<th>Agendado</th>
<th>Considerações</th>
</tr>

<tr>
<td><p>Arquivo para armazenamento do Azure</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">PowerShell do Azure</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Importação/Exportação do Azure</a></li>
</ul>
</td>
<td><p>N/D</p>
</td>
<td><p>Use Importação/Exportação do Azure em vez de AzCopy, acp e PowerShell do Azure quando a transferência de dados pela rede for impraticável. O serviço pode levar vários dias além do envio do disco para o data center.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server para o Banco de Dados SQL do Azure</li>
<li>Banco de Dados do SQL Azure com o SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Fábrica de dados do Azure</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">Ferramentas de migração do Banco de Dados SQL</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Fábrica de dados do Azure</a></p>
</td>
<td><p>Sugira usar a Fábrica de dados do Azure (visualização), em vez de ferramentas de migração, que oferece conversão de formato e recuperação automática de trabalho em casos excepcionais. Se o SQL Server for local, o gateway de gerenciamento de dados é necessário para conexão ao SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Armazenamento do Azure para o Banco de Dados SQL do Azure</li>
<li>Banco de Dados SQL do Azure para o armazenamento do Azure</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Fábrica de dados do Azure</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Fábrica de dados do Azure</a></p>
</td>
<td><p>A Fábrica de dados do Azure (visualização) oferece a conversão de formato e recuperação automática de trabalho em casos excepcionais. Se o SQL Server for local, o gateway de gerenciamento de dados é necessário para conexão ao SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server para Sincronização de Dados do SQL</li>
<li>Sincronização de Dados do SQL para o Banco de Dados SQL do Azure</li>
</ul>
</td>
<td><p>N/D</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">Sincronização de Dados do SQL</a></p>
</td>
<td><p>A Sincronização de Dados SQL (visualização) sincroniza os dados por grupos de sincronização que definem os bancos de dados, as tabelas e as colunas a sincronizar, bem como a agenda de sincronização.</p>
</td>
</tr>

</table>



### Mover TBs de dados

<table border="1">
<tr>
<th>Mover dados</th>
<th>Uma vez</th>
<th>Agendado</th>
<th>Considerações</th>
</tr>

<tr>
<td><p>Arquivo para armazenamento do Azure</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Importação/Exportação do Azure</a></p>
</td>
<td><p>N/D</p>
</td>
<td><p>O serviço pode levar vários dias além do tempo necessário para o disco chegar ao data center.</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Serviço de Importação/Exportação do Azure para armazenamento de Blob]: #blob
[Utilitário AZCopy]: #azcopy-utility
[PowerShell do Azure]: #ps
[Fábrica de dados do Azure (visualização)]: #data-factory
[Ferramentas de migração do Banco de Dados SQL do Azure]: #tools
[Sincronização de Dados do SQL Azure (visualização)]: #data-sync
[Hubs de eventos do Azure]: #event-hubs
[Outras opções para transferência de dados]: #other
[Escolha a opção de transferência de dados correta]: #choose

<!--Image references-->
[decisão]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[importar-exportar]: storage-import-export-service.md
[azcopy]: storage-use-azcopy.md
[carregar]: hdinsight-upload-data.md#powershell
[instalar]: install-configure-powershell.md
[iniciar]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copiar]: data-factory-copy-activity.md
[introdução]: data-factory-introduction.md
[sql-importar]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copiar]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrar]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[assistente]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[usar-assistente]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrar]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[visão geral]: http://msdn.microsoft.com/library/dn836025.aspx
[fluxo]: stream-analytics-introduction.md
[sincronizar]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[híbrido]: integration-hybrid-connection-overview.md

<!--HONumber=52-->