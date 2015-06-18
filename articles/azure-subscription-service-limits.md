<properties 
	pageTitle="Assinatura do Microsoft Azure e limites de serviços, cotas e restrições" 
	description="Fornece uma lista de assinaturas comuns do Azure e limites de serviço, cotas e restrições. Isso inclui informações sobre como aumentar os limites junto com os valores máximos."
	services="" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="jroth"/>

# Assinatura do Azure e limite de serviços, cotas e restrições

## Visão geral

Este documento especifica alguns dos limites mais comuns do Microsoft Azure. Observe que isso não cobre atualmente todos os serviços do Azure. Com o tempo, esses limites serão ampliados e atualizados para uma maior cobertura da plataforma.

> [AZURE.NOTE] Se você deseja aumentar o limite acima do **Limite padrão**, você pode [abrir uma solicitação no suporte ao cliente online sem custo][azurelimitsblogpost]. Os limites não podem ser aumentados acima do valor de **Limite máximo** nas tabelas abaixo. Se não houver nenhuma coluna **Limite máximo**, o recurso especificado não tem limites ajustáveis.

## Limites de assinatura

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
   <th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
   <td valign="middle"><p>Núcleos por <a href="http://msdn.microsoft.com/library/azure/hh531793.aspx">assinatura</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10.000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/gg456328.aspx">Coadministradores</a> por assinatura</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/storage-create-storage-account/">Contas de armazenamento</a> por assinatura</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Serviços de nuvem</a> por assinatura</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Redes virtuais</a> por assinatura<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/jj157100.aspx">Redes locais</a> por assinatura</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Servidores de banco de dados SQL por assinatura</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>Bancos de dados SQL por servidor</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>Servidores DNS por assinatura</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>IPs reservados por assinatura</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Certificados de serviço hospedado por assinatura</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156085.aspx">Grupos de afinidade</a> por assinatura</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/">Grupos de recursos</a> por assinatura</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/services/batch/">Visualização de lote</a> contas por região por assinatura</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>Instâncias super pequenas contam como um núcleo para o limite do núcleo, apesar de utilizarem um núcleo parcial.

<sup>2</sup>Cada rede virtual oferece suporte a um único gateway de rede virtual.

## Limites do serviço de nuvem

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
   <th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Funções Web/de trabalho por implantação<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint">Pontos de extremidade de entrada de instância</a> por implantação</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint">Pontos de extremidade de entrada</a> por implantação</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint">Pontos de extremidade internos</a> por implantação</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Cada serviço de nuvem com funções de trabalho/Web pode ter duas implantações, uma para produção e uma de preparo. Observe também que esse limite se refere ao número de funções distintas (configuração) e não ao número de instâncias por função (escala). 

## Limites de máquinas virtuais

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
   <th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Máquinas virtuais</a> por serviço de nuvem<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Pontos de extremidade de entrada por serviço de nuvem<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Quando você cria uma máquina virtual, um serviço de nuvem é criado automaticamente para conter a máquina. Então, é possível adicionar várias máquinas virtuais no mesmo serviço de nuvem.

<sup>2</sup>Os pontos de extremidade são utilizados para permitir a comunicação com as máquinas virtuais externas que contêm o serviço de nuvem. As máquinas virtuais dentro do mesmo serviço de nuvem permitem automaticamente a comunicação entre todas as portas TCP e UDP para comunicação interna.

## Limites de Websites

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## Limites de rede

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
   <th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
   <td valign="middle"><p>Total de máquinas<sup>1</sup> por <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Rede Virtual</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Conexões TCP simultâneas para uma máquina virtual ou instância de função</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Listas de Controle de Acesso (ACLs) por ponto de extremidade<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sites de rede local por rede virtual</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>O número total de máquinas inclui máquinas virtuais e instâncias de funções de trabalho/Web.

<sup>2</sup>Cada rede virtual oferece suporte a um único [gateway de rede virtual][gateway].

<sup>3</sup>ACL tem suporte nos pontos de extremidade de entrada para máquinas virtuais. Para funções de trabalho/Web, ACL tem suporte nos pontos de extremidade de entrada de instância e na entrada.

## Limites de armazenamento

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso<sup>1</sup></th>
   <th align="left" valign="middle">Limite padrão</th>
</tr>
<tr>
   <td valign="middle"><p>TB por conta de armazenamento</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamanho máximo de um único contêiner de blob, tabela ou fila</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento</p></td>
   <td valign="middle"><p>O único limite é a capacidade de conta de armazenamento de 500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamanho máximo de um compartilhamento de arquivo</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de arquivos em um compartilhamento de arquivo</p></td>
   <td valign="middle"><p>O único limite é a capacidade total de 5 TB do compartilhamento de arquivo</p></td>
</tr>
<tr>
   <td valign="middle"><p>IOPS máx. de 8 KB por disco persistente (Camada Básica)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>IOPS máx. de 8 KB por disco persistente (Camada Padrão)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Taxa de solicitação total (presumindo um tamanho de objeto de 1KB) por conta de armazenamento</p></td>
   <td valign="middle"><p>Até 20.000 entidades ou mensagens por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimento de destino por blob único</p></td>
   <td valign="middle"><p>Até 60 MB por segundo, ou até 500 solicitações por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimento de destino por fila única (mensagens de 1 KB)</p></td>
   <td valign="middle"><p>Até 2000 mensagens por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimento de destino por partição de tabela única (entidades de 1 KB)</p></td>
   <td valign="middle"><p>Até 2000 entidades por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ingresso máximo por conta de armazenamento (regiões dos EUA)</p></td>
   <td valign="middle"><p>10 Gbps se GRS<sup>3</sup> habilitados, 20 Gbps por LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Saída máxima por conta de armazenamento (regiões dos EUA)</p></td>
   <td valign="middle"><p>20 Gbps se GRS<sup>3</sup> habilitados, 30 Gbps por LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ingresso máximo por conta de armazenamento (regiões da Ásia e Europa)</p></td>
   <td valign="middle"><p>5 Gbps se GRS<sup>3</sup> habilitados, 10 Gbps por LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Saída máxima por conta de armazenamento (regiões da Ásia e Europa)</p></td>
   <td valign="middle"><p>10 Gbps se GRS<sup>3</sup> habilitados, 15 Gbps por LRS</p></td>
</tr>
</table>

<sup>1</sup>Para obter mais detalhes sobre esses limites, consulte [Metas de desempenho e de escalabilidade de armazenamento do Azure][storagelimits]. 

<sup>2</sup>Para máquinas virtuais na Camada Básica, não coloque mais de 66 VHDs altamente utilizados em uma conta de armazenamento para evitar o limite de taxa de solicitação total de 20.000 (20.000/300). Para máquinas virtuais na Camada Padrão, não coloque mais de 40 VHDs altamente utilizados em uma conta de armazenamento (20.000/500). Para obter mais informações, veja [Tamanhos de máquina virtual e serviço de nuvem para o Azure][vmsizes]. 

<sup>3</sup>GRS significa [armazenamento geograficamente redundante][georedundantstorage]. LRS significa [armazenamento localmente redundante][locallyredundantstorage]. Observe que o GRS também possui redundância local.

## Limites de visualização de lote

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

## Limites de visualização do Banco de Dados de Documentos

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## Limites de banco de dados SQL

Para obter os limites de banco de dados SQL, consulte os seguintes tópicos:

 - [Faixas de serviço de banco de dados SQL do Azure (edições)][sqltiers]
 - [Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho][sqltiersperflevels]
 - [Cotas de Unidade de Rendimento do Banco de Dados (DTU)][sqlDTU]
 - [Limites de recurso de banco de dados SQL][sqldblimits]

## Limites de serviços de mídia

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## Limites de barramento de serviço

[AZURE.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## Limites do Active Directory

Para o Azure Active Directory (AD), consulte o tópico a seguir:

 - [Restrições e limites de serviços do Active Directory do Azure][adlimitsandrestrictions]

## Consulte também

[Entendendo os limites e aumentos do Azure][azurelimitsblogpost]

[Máquinas virtuais e tamanhos de serviço de nuvem para o Azure][vmsizes]

[customersupportfaq]: http://azure.microsoft.com/support/faq/
[azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[gateway]: http://msdn.microsoft.com/library/azure/jj156210.aspx 
[storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
[georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
[sqldblimits]: http://msdn.microsoft.com/library/azure/dn338081.aspx
[sqltiers]: http://msdn.microsoft.com/library/azure/dn741340.aspx
[sqltiersperflevels]: http://msdn.microsoft.com/library/azure/dn741336.aspx
[sqlDTU]: http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs
[vmsizes]: http://msdn.microsoft.com/library/azure/dn197896.aspx
[georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/library/azure/dn764971.aspx

<!--HONumber=47-->
