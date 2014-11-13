<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Assinatura do Microsoft Azure e limites de servi&ccedil;os, cotas e restri&ccedil;&otilde;es" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Oferece uma lista de assinaturas e limites de servi&ccedil;o comuns do Azure juntamente com os valores m&aacute;ximos." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Assinatura do Azure e limite de serviços, cotas e restrições

O documento a seguir especifica alguns dos limites mais comuns do Microsoft Azure. Observe que esse documento não cobre atualmente todos os serviços do Azure. Com o tempo, esses limites serão ampliados e atualizados para uma maior cobertura da plataforma.

-   [Limites de assinatura][Limites de assinatura]
-   [Limites do serviço de nuvem][Limites do serviço de nuvem]
-   [Limites de máquinas virtuais][Limites de máquinas virtuais]
-   [Limites de Websites][Limites de Websites]
-   [Limites de rede][Limites de rede]
-   [Limites de armazenamento][Limites de armazenamento]
-   [Limites de visualização do Banco de Dados de Documentos][Limites de visualização do Banco de Dados de Documentos]
-   [Limites de banco de dados SQL][Limites de banco de dados SQL]
-   [Limites de serviços de mídia][Limites de serviços de mídia]
-   [Limites de barramento de serviço][Limites de barramento de serviço]

> [WACOM.NOTE] Se você deseja aumentar o limite acima do **Limite padrão**, você pode [abrir uma solicitação no suporte ao cliente online sem custo][abrir uma solicitação no suporte ao cliente online sem custo]. Os limites não podem ser aumentados acima do valor de **Limite máximo** nas tabelas abaixo. Se não houver nenhuma coluna **Limite máximo**, o recurso especificado não tem limites ajustáveis.

## <a name="subscription"></a>Limites de assinatura

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Limite padrão</th>
<th align="left">Limite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Núcleos por <a href="http://msdn.microsoft.com/pt-br/library/azure/hh531793.aspx">assinatura</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10.000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/pt-br/library/azure/gg456328.aspx">Coadministradores</a> por assinatura</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/pt-br/documentation/articles/storage-whatis-account/">Contas de armazenamento</a> por assinatura</p></td>
<td align="left"><p>100</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-what-is/">Serviços de nuvem</a> por assinatura</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Redes virtuais</a> por assinatura<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/pt-br/library/jj157100.aspx">Redes locais</a> por assinatura</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Bancos de dados SQL por assinatura</p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Servidores do banco de dados SQL lógico por assinatura</p></td>
<td align="left"><p>6</p></td>
<td align="left"><p>150</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Servidores DNS por assinatura</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>IPs reservados por assinatura</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Certificados de serviço hospedado por assinatura</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/pt-br/library/azure/jj156085.aspx">Grupos de afinidade</a> por assinatura</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Instâncias super pequenas contam como um núcleo para o limite do núcleo, apesar de utilizarem um núcleo parcial.

<sup>2</sup>Cada rede virtual oferece suporte a um único gateway de rede virtual.

## <a name="webworkerlimits"></a>Limites do serviço de nuvem

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Limite padrão</th>
<th align="left">Limite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-what-is/">Funções de trabalho/Web por implantação<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/pt-br/library/gg557552.aspx#InstanceInputEndpoint">Pontos de extremidade de entrada de instância</a> por implantação</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/pt-br/library/gg557552.aspx#InputEndpoint">Pontos de extremidade de entrada</a> por implantação</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/pt-br/library/gg557552.aspx#InternalEndpoint">Pontos de extremidade internos</a> por implantação</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Cada serviço de nuvem com funções de trabalho/Web pode ter duas implantações, uma para produção e uma de preparo. Observe também que esse limite se refere ao número de funções distintas (configuração) e não ao número de instâncias por função (escala).

## <a name="vmlimits"></a>Limites de máquinas virtuais

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Limite padrão</th>
<th align="left">Limite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/pt-br/documentation/services/virtual-machines/">Máquinas virtuais</a> por serviço de nuvem<sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Pontos de extremidade por serviço de nuvem<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Quando você cria uma máquina virtual, um serviço de nuvem é criado automaticamente para conter a máquina. Então, é possível adicionar várias máquinas virtuais no mesmo serviço de nuvem.

<sup>2</sup>Os pontos de extremidade são utilizados para permitir a comunicação com as máquinas virtuais externas que contêm o serviço de nuvem. As máquinas virtuais dentro do mesmo serviço de nuvem permitem automaticamente a comunicação entre todas as portas TCP e UDP para comunicação interna.

## <a name="websiteslimits"></a>Limites de Websites

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## <a name="networkinglimits"></a>Limites de rede

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Limite padrão</th>
<th align="left">Limite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Máquinas totais<sup>1</sup> por <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Rede virtual</a><sup>2</sup></p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>Conexões TCP simultâneas para uma máquina virtual ou instância de função</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Listas de Controle de Acesso (ACLs) por ponto de extremidade<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Sites de rede local por rede virtual</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>O número total de máquinas inclui máquinas virtuais e instâncias de funções de trabalho/Web.

<sup>2</sup>Cada rede virtual oferece suporte a um único [gateway de rede virtual][gateway de rede virtual].

<sup>3</sup>ACL tem suporte nos pontos de extremidade de entrada para máquinas virtuais. Para funções de trabalho/Web, ACL tem suporte nos pontos de extremidade de entrada de instância e na entrada.

## <a name="storagelimits"></a>Limites de armazenamento

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso<sup>1</sup></th>
<th align="left">Limite padrão</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>TB por conta de armazenamento</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="even">
<td align="left"><p>Tamanho máximo de um único contêiner de blob, tabela ou fila</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento</p></td>
<td align="left"><p>O único limite é a capacidade de conta de armazenamento de 500 TB</p></td>
</tr>
<tr class="even">
<td align="left"><p>Tamanho máximo de um compartilhamento de arquivo</p></td>
<td align="left"><p>5 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Número máximo de arquivos em um compartilhamento de arquivo</p></td>
<td align="left"><p>O único limite é a capacidade total de 5 TB do compartilhamento de arquivo</p></td>
</tr>
<tr class="even">
<td align="left"><p>IOPS máx. de 8 KB por disco persistente (Camada Básica)</p></td>
<td align="left"><p>300<sup>2</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>IOPS máx. de 8 KB por disco persistente (Camada Padrão)</p></td>
<td align="left"><p>500<sup>2</sup></p></td>
</tr>
<tr class="even">
<td align="left"><p>Taxa de solicitação total (presumindo um tamanho de objeto de 1KB) por conta de armazenamento</p></td>
<td align="left"><p>Até 20.000 entidades ou mensagens por segundo</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Rendimento de destino por blob único</p></td>
<td align="left"><p>Até 60 MB por segundo, ou até 500 solicitações por segundo</p></td>
</tr>
<tr class="even">
<td align="left"><p>Rendimento de destino por fila única (mensagens de 1 KB)</p></td>
<td align="left"><p>Até 2000 mensagens por segundo</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Rendimento de destino por partição de tabela única (entidades de 1 KB)</p></td>
<td align="left"><p>Até 2000 entidades por segundo</p></td>
</tr>
<tr class="even">
<td align="left"><p>Ingresso máximo por conta de armazenamento (regiões dos EUA)</p></td>
<td align="left"><p>10 Gbps se o GRS<sup>3</sup> estiver habilitado, 20 Gbps para LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Saída máxima por conta de armazenamento (regiões dos EUA)</p></td>
<td align="left"><p>20 Gbps se o GRS<sup>3</sup> estiver habilitado, 30 Gbps para LRS</p></td>
</tr>
<tr class="even">
<td align="left"><p>Ingresso máximo por conta de armazenamento (regiões da Ásia e Europa)</p></td>
<td align="left"><p>5 Gbps se o GRS<sup>3</sup> estiver habilitado, 10 Gbps para LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Saída máxima por conta de armazenamento (regiões da Ásia e Europa)</p></td>
<td align="left"><p>10 Gbps se o GRS<sup>3</sup> estiver habilitado, 15 Gbps para LRS</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Para obter mais detalhes sobre esses limites, consulte [Metas de desempenho e de escalabilidade de armazenamento do Azure][Metas de desempenho e de escalabilidade de armazenamento do Azure].

<sup>2</sup>Para máquinas virtuais na Camada Básica, não coloque mais de 66 VHDs altamente utilizados em uma conta de armazenamento para evitar o limite de taxa de solicitação total de 20.000 (20.000/300). Para máquinas virtuais na Camada Padrão, não coloque mais de 40 VHDs altamente utilizados em uma conta de armazenamento (20.000/500). Para obter mais informações, veja [Tamanhos de máquina virtual e serviço de nuvem para o Azure][Tamanhos de máquina virtual e serviço de nuvem para o Azure].

<sup>3</sup>GRS significa [Armazenamento com redundância geográfica][Armazenamento com redundância geográfica]. LRS significa [Armazenamento com redundância local][Armazenamento com redundância local]. Observe que o GRS também possui redundância local.

## <a name="documentdblimits"></a>Limites de visualização do Banco de Dados de Documentos

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## <a name="sqldblimits"></a>Limites de banco de dados SQL

Para obter os limites de banco de dados SQL, consulte os seguintes tópicos:

-   [Faixas de serviço de banco de dados SQL do Azure (edições)][Faixas de serviço de banco de dados SQL do Azure (edições)]
-   [Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho][Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho]
-   [Cotas de Unidade de Rendimento do Banco de Dados (DTU)][Cotas de Unidade de Rendimento do Banco de Dados (DTU)]
-   [Limites de recurso de banco de dados SQL][Limites de recurso de banco de dados SQL]

## <a name="mediaserviceslimits"></a>Limites de serviços de mídia

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## <a name="servicebuslimits"></a>Limites de barramento de serviço

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## <a name="seealso"></a>Consulte também

[Entendendo os limites e aumentos do Azure][abrir uma solicitação no suporte ao cliente online sem custo]

[Tamanhos de máquinas virtuais e serviço de nuvem para o Azure][Tamanhos de máquina virtual e serviço de nuvem para o Azure]

  [Limites de assinatura]: #subscription
  [Limites do serviço de nuvem]: #webworkerlimits
  [Limites de máquinas virtuais]: #vmlimits
  [Limites de Websites]: #websiteslimits
  [Limites de rede]: #networkinglimits
  [Limites de armazenamento]: #storagelimits
  [Limites de visualização do Banco de Dados de Documentos]: #documentdblimits
  [Limites de banco de dados SQL]: #sqldblimits
  [Limites de serviços de mídia]: #mediaserviceslimits
  [Limites de barramento de serviço]: #servicebuslimits
  [abrir uma solicitação no suporte ao cliente online sem custo]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway de rede virtual]: http://msdn.microsoft.com/pt-br/library/azure/jj156210.aspx
  [Metas de desempenho e de escalabilidade de armazenamento do Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Tamanhos de máquina virtual e serviço de nuvem para o Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn197896.aspx
  [Armazenamento com redundância geográfica]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [Armazenamento com redundância local]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [Faixas de serviço de banco de dados SQL do Azure (edições)]: http://msdn.microsoft.com/pt-br/library/azure/dn741340.aspx
  [Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho]: http://msdn.microsoft.com/pt-br/library/azure/dn741336.aspx
  [Cotas de Unidade de Rendimento do Banco de Dados (DTU)]: http://msdn.microsoft.com/pt-br/library/azure/ee336245.aspx#DTUs
  [Limites de recurso de banco de dados SQL]: http://msdn.microsoft.com/pt-br/library/azure/dn338081.aspx
