<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Assinatura do Azure e limite de serviços, cotas e restrições

O documento a seguir especifica alguns dos limites mais comuns do Microsoft Azure. Observe que esse documento não cobre atualmente todos os serviços do Azure. Com o tempo, esses limites serão ampliados e atualizados para uma maior cobertura da plataforma.

-   [Limites de assinatura][]
-   [Limites de trabalhos da Web][]
-   [Limites de máquinas virtuais][]
-   [Limites de rede][]
-   [Limites de armazenamento][]
-   [Limites de banco de dados SQL][]

> [WACOM.NOTE] Se você deseja aumentar o limite acima do **Limite padrão**, abra uma solicitação no [suporte ao cliente][]. Os limites não podem ser aumentados acima do valor de **Limite máximo** nas tabelas abaixo. Se não houver nenhuma coluna **Limite máximo**, o recurso especificado não tem limites ajustáveis.

## <a name="subscription"></a>Limites de assinatura

<table cellspacing="0" border="1">
<tr>
<th align="left">Recurso</th>
<th align="left">Limite padrão</th>
<th align="left">Limite máximo</th>
</tr>
<tr>
<td valign="middle"><p>Núcleos por <a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">assinatura</a><sup>1</sup></p></td>
<td valign="middle"><p>20</p></td>
<td valign="middle"><p>10,000</p></td>
</tr>
<tr>
<td valign="middle"><p><a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">Coadministradores</a> por assinatura</p></td>
<td valign="middle"><p>200</p></td>
<td valign="middle"><p>200</p></td>
</tr>
<tr>
<td valign="middle"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/">Contas de armazenamento</a> por assinatura</p></td>
<td valign="middle"><p>20</p></td>
<td valign="middle"><p>50</p></td>
</tr>
<tr class="even">
<td valign="middle"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Serviços de nuvem</a> por assinatura</p></td>
<td valign="middle"><p>20</p></td>
<td valign="middle"><p>200</p></td>
</tr>
<tr>
<td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Redes virtuais</a> por assinatura<sup>2</sup></p></td>
<td valign="middle"><p>10</p></td>
<td valign="middle"><p>100</p></td>
</tr>
<tr>
<td valign="middle"><p><a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">Redes locais</a> por assinatura</p></td>
<td valign="middle"><p>10</p></td>
<td valign="middle"><p>100</p></td>
</tr>
<tr>
<td valign="middle"><p>Servidores DNS por assinatura</p></td>
<td valign="middle"><p>9</p></td>
<td valign="middle"<p>100</p></td>
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
<td valign="middle"><p><a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">Grupos de afinidade</a> por assinatura</p></td>
<td valign="middle"><p>256</p></td>
<td valign="middle"><p>256</p></td>
</tr>
</table>

<sup>1</sup>Instâncias super pequenas contam como um núcleo para o limite do núcleo, apesar de utilizarem um núcleo parcial.

<sup>2</sup>Cada rede virtual oferece suporte a um único gateway de rede virtual.

## <a name="webworkerlimits"></a>Limites de trabalho/Web

<table cellspacing="0" border="1">
<tr>
<th align="left" valign="middle">Recurso</th>
<th align="left" valign="middle">Limite padrão</th>
<th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
<td valign="middle""><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Funções de trabalho/Web por implantação<sup>1</sup></a></p></td>
<td valign="middle"><p>25</p></td>
<td valign="middle"><p>25</p></td>
</tr>
<tr>
<td valign="middle"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">Pontos de extremidade de entrada de instância</a> por implantação</p></td>
<td valign="middle"><p>25</p></td>
<td valign="middle"><p>25</p></td>
</tr>
<tr>
<td valign="middle"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">Pontos de extremidade de entrada</a> por implantação</p></td>
<td valign="middle"><p>25</p></td>
<td valign="middle"><p>25</p></td>
</tr>
<tr>
<td valign="middle"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">Pontos de extremidade internos</a> por implantação</p></td>
<td valign="middle"><p>25</p></td>
<td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Cada serviço de nuvem com funções de trabalho/Web pode ter duas implantações, uma para produção e uma de preparo.

## <a name="vmlimits"></a>Limites de máquinas virtuais

<table cellspacing="0" border="1">
<tr class="header">
<th align="left" valign="middle">Recurso</th>
<th align="left" valign="middle">Limite padrão</th>
<th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
<td valign="middle"><p><a href="http://azure.microsoft.com/en-us/documentation/services/virtual-machines/">Máquinas virtuais</a> por serviço de nuvem<sup>1</sup></p></td>
<td valign="middle"><p>50</p></td>
<td valign="middle"><p>50</p></td>
</tr>
<tr>
<td valign="middle"><p>Pontos de extremidade por serviço de nuvem<sup>2</sup></p></td>
<td valign="middle"><p>150</p></td>
<td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Quando você cria uma máquina virtual, um serviço de nuvem é criado automaticamente para conter a máquina. Então, é possível adicionar várias máquinas virtuais no mesmo serviço de nuvem.

<sup>2</sup>Os pontos de extremidade são utilizados para permitir a comunicação com as máquinas virtuais externas que contêm o serviço de nuvem. As máquinas virtuais dentro do mesmo serviço de nuvem permitem automaticamente a comunicação entre todas as portas TCP e UDP para comunicação interna.

## <a name="networkinglimits"></a>Limites de rede

<table cellspacing="0" border="1">
<tr>
<th align="left" valign="middle">Recurso</th>
<th align="left" valign="middle">Limite padrão</th>
<th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
<td align="left"><p>Máquinas totais<sup>1</sup> por <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Rede virtual</a><sup>2</sup></p></td>
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

<sup>2</sup>Cada rede virtual oferece suporte a um único [gateway de rede virtual][].

<sup>3</sup>ACL tem suporte nos pontos de extremidade de entrada para máquinas virtuais. Para funções de trabalho/Web, ACL tem suporte nos pontos de extremidade de entrada de instância e na entrada.

## <a name="storagelimits"></a>Limites de armazenamentos<sup>1</sup>

<table cellspacing="0" border"1">
<tr>
<th align="left" valign="middle">Recurso</th>
<th align="left" valign="middle">Limite padrão</th>
</tr>
<tr>
<td valign="middle"><p>TB por conta de armazenamento<sup>2</sup></p></td>
<td valign="middle"><p>500</p></td>
</tr>
<td valign="middle"><p>IOPS máximo para disco persistente</p></td>
<td valign="middle"><p>500<sup>3</sup></p></td>
</tr>
<tr>
<td valign="middle"><p>IOPS máximo por conta de armazenamento</p></td>
<td valign="middle"><p>20,000</p></td>
</tr>
<tr>
<td valign="middle"><p>Ingresso máximo por conta de armazenamento (regiões dos E.U.A.)</p></td>
<td valign="middle"><p>10 Gbps se o GRS<sup>4</sup> estiver habilitado, 20 Gbps se estiver desabilitado</p></td>
</tr>
<tr valign="middle">
<td valign="middle"><p>Ingresso máximo por conta de armazenamento (regiões da Ásia e Europa)</p></td>
<td valign="middle"><p>5 Gbps se o GRS<sup>4</sup> estiver habilitado, 10 Gbps se estiver desabilitado</p></td>
</tr>
<tr>
<td valign="middle"><p>Saída máxima por conta de armazenamento (regiões dos E.U.A.)</p></td>
<td valign="middle"><p>20 Gbps se o GRS<sup>4</sup> estiver habilitado, 30 Gbps se estiver desabilitado</p></td>
</tr>
<tr>
<td valign="middle"><p>Saída máxima por conta de armazenamento (regiões da Ásia e Europa)</p></td>
<td valign="middle"><p>10 Gbps se o GRS<sup>4</sup> estiver habilitado, 15 Gbps se estiver desabilitado</p></td>
</tr>
</table>

<sup>1</sup>Para obter mais detalhes sobre esses limites, consulte [Metas de desempenho e de escalabilidade de armazenamento do Azure][].

<sup>2</sup>Para páginas de blobs, apenas páginas que estão em uso acumulam uso da capacidade. Por exemplo, uma máquina virtual com um VHD de 127 GB, mas com apenas 30 GB de uso pelo sistema operacional, é apenas cobrada pelo uso da parte de 30 GB no VHD, não pelos 127 GBs totais.

<sup>3</sup>Não coloque mais de 40 VHDs altamente utilizados em uma conta para evitar o limite de 20.000 IOPS.

<sup>4</sup>Conta de armazenamento geográfica redundante

## <a name="sqldblimits"></a>Limites de banco de dados SQL

Para obter os limites de banco de dados SQL, consulte os seguintes tópicos:

-   [Faixas de serviço de banco de dados SQL do Azure (edições)][]
-   [Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho][]
-   [Limites de recurso de banco de dados SQL][]

## <a name="seealso"></a> Consulte também

[Tamanhos de máquinas virtuais e serviço de nuvem para o Azure][]

[Metas de desempenho e escalabilidade do Armazenamento do Azure][Metas de desempenho e de escalabilidade de armazenamento do Azure]

[Faixas de serviço de banco de dados SQL do Azure (edições)][]

[Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho][]

[Limites de recurso de banco de dados SQL][]

  [Limites de assinatura]: #subscription
  [Limites de trabalhos da Web]: #webworkerlimits
  [Limites de máquinas virtuais]: #vmlimits
  [Limites de rede]: #networkinglimits
  [Limites de armazenamento]: #storagelimits
  [Limites de banco de dados SQL]: #sqldblimits
  [suporte ao cliente]: http://azure.microsoft.com/en-us/support/faq/
  [assinatura]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [Coadministradores]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [Contas de armazenamento]: http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/
  [Serviços de nuvem]: http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/
  [Redes virtuais]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [Redes locais]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [Grupos de afinidade]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [Pontos de extremidade de entrada de instância]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [Pontos de extremidade de entrada]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [Pontos de extremidade internos]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [Máquinas virtuais]: http://azure.microsoft.com/en-us/documentation/services/virtual-machines/
  [gateway de rede virtual]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Metas de desempenho e de escalabilidade de armazenamento do Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Faixas de serviço de banco de dados SQL do Azure (edições)]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [Limites de recurso de banco de dados SQL]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Tamanhos de máquinas virtuais e serviço de nuvem para o Azure]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
