<properties 
   pageTitle="Metas de desempenho e escalabilidade do Armazenamento do Azure | Azure"
   description="Saiba mais sobre as metas de desempenho e escalabilidade para uma conta do Armazenamento do Azure, incluindo a capacidade, taxa de solicitação e largura de banda de entrada e saída. Entender as metas de desempenho para partições em de cada um dos serviços do Armazenamento do Azure."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" /> <tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="04/24/2015"
   ms.author="tamram" />

# Metas de desempenho e escalabilidade do Armazenamento do Azure

Este tópico descreve os tópicos de desempenho e escalabilidade do Armazenamento do Microsoft Azure. Para obter um resumo de outros limites do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

>[AZURE.NOTE]Todas as contas de armazenamento são executadas na nova topologia de rede simples e dão suporte às metas de escalabilidade e desempenho descritas abaixo, independentemente de quando foram criadas. Para saber mais sobre a arquitetura de rede simples do Armazenamento do Azure e sobre escalabilidade, confira o Blog da Equipe de Armazenamento do Azure.

<!-- -->

>[AZURE.IMPORTANT]As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo. Teste o serviço para determinar se o desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.

>Quando seu aplicativo atingir o limite de uma partição, o Armazenamento do Azure passará a retornar respostas com o código de erro 503 (servidor ocupado) ou o código de erro 500 (tempo limite da operação). Quando isso ocorre, o aplicativo deve usar uma política de retirada exponencial para repetições. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.

Se as necessidades de seu aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e faça o particionamento dos seus objetos de dados nessas contas de armazenamento. Uma única assinatura do Azure pode ter 100 contas de armazenamento. Consulte [Detalhes de preços de armazenamento](http://azure.microsoft.com/pricing/details/storage/) para obter informações sobre o preço por volume.

## Metas de escalabilidade para Contas de armazenamento

As metas de escalabilidade de uma conta de armazenamento dependem da região onde ela foi criada. As seções a seguir descrevem as metas de escalabilidade para regiões dos Estados Unidos e para regiões da Europa e da Ásia.

Para saber mais sobre as metas de arquitetura e de escalabilidade para contas de armazenamento, confira o [Blog da Equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/04/windows-azure-s-flat-network-storage-and-2012-scalability-targets.aspx).

### Metas de escalabilidade para contas de armazenamento padrão nas regiões dos EUA

A tabela a seguir descreve as metas de escalabilidade para contas de armazenamento padrão nas regiões dos EUA, com base no nível de redundância escolhido:

<table>
<tr>
<th>Capacidade total da conta</th>
<th>Taxa de solicitação total (supondo um tamanho de objeto de 1KB)</th>
<th>Largura de banda total para uma conta de armazenamento localmente redundante</th>
<th>Largura de banda total para uma conta de armazenamento localmente redundante</th>
<tr>
<td>500 TB</td>
<td>Até 20.000 entidades ou mensagens por segundo</td>
<td><ul>
<li>*Entrada: até 10 gigabits por segundo</li>
<li>*Saída: até 20 gigabits por segundo</li>
</ol>
</td>
<td><ul>
<li>*Entrada: até 20 gigabits por segundo</li>
<li>*Saída: até 30 gigabits por segundo</li>
</tr>
</table>

*Entrada refere-se a todos os dados (solicitações) que estão sendo enviados para uma conta de armazenamento. 

*Saída refere-se a todos os dados (respostas) que estão sendo recebidos de uma conta de armazenamento. 

### Metas de escalabilidade para contas de armazenamento padrão nas regiões da Europa e da Ásia

A tabela a seguir descreve as metas de escalabilidade para contas de armazenamento nas regiões da Europa e da Ásia, com base no nível de redundância escolhido:

<table>
<tr>
<th>Capacidade total da conta</th>
<th>Taxa de solicitação total (supondo um tamanho de objeto de 1KB)</th>
<th>Largura de banda total para uma conta de armazenamento localmente redundante</th>
<th>Largura de banda total para uma conta de armazenamento localmente redundante</th>
<tr>
<td>500 TB</td>
<td>Até 20.000 entidades ou mensagens por segundo</td>
<td><ul>
<li>*Entrada: até 5 gigabits por segundo</li>
<li>*Saída: até 10 gigabits por segundo</li>
</ol>
</td>
<td><ul>
<li>*Entrada: até 10 gigabits por segundo</li>
<li>*Saída: até 15 gigabits por segundo</li>
</tr>
</table>

*Entrada refere-se a todos os dados (solicitações) que estão sendo enviados para uma conta de armazenamento. 

*Saída refere-se a todos os dados (respostas) que estão sendo recebidos de uma conta de armazenamento. 

### Metas de escalabilidade para Contas de armazenamento Premium

A tabela a seguir descreve as metas de escalabilidade para Contas de armazenamento Premium disponíveis nas regiões Oeste dos EUA, Leste dos EUA 2 e Europa Ocidental:

<table>
<tr>
<th>Capacidade total da conta</th>
<th>Largura de banda total para uma Conta de armazenamento Premium localmente redundante</th>
<tr>
<td><ul>
<li>Capacidade do disco: 35 TB</li>
<li>Capacidade de instantâneo: 10TB</li>
</td>
<td>Até 50 gigabits para Entrada + Saída</td>
</table>

*Entrada refere-se a todos os dados (solicitações) que estão sendo enviados para uma conta de armazenamento. 

*Saída refere-se a todos os dados (respostas) que estão sendo recebidos de uma conta de armazenamento.

Para saber mais sobre o discos de Armazenamento Premium, confira [Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](storage-premium-storage-preview-portal.md).

## Partições no Armazenamento do Azure

A tabela a seguir descreve as metas de desempenho para uma única partição de cada serviço:

<table>
<tr>
<th>Rendimento de destino por blob único</th>
<th>Rendimento de destino por fila única (mensagens de 1 KB)</th>
<th>Rendimento de destino por partição de tabela única (entidades de 1 KB)</th>
<th>Rendimento de destino por arquivo único (Visualização)</th><tr>
<td>Até 60 MB por segundo, ou até 500 solicitações por segundo</td>
<td>Até 2000 mensagens por segundo</td>
<td>Até 2000 entidades por segundo</td>
<td>Até 60 MB por segundo</td>
</table>

Cada objeto com dados armazenado no Armazenamento do Azure (blobs, mensagens, entidades e arquivos) pertence a uma partição e é identificado por uma chave de partição. A partição determina como o Armazenamento do Azure equilibra as cargas de blobs, mensagens, entidades e arquivos em servidores a fim de atender às necessidades de tráfego desses objetos. A chave de partição é exclusiva dentro da conta de armazenamento e é usada para localizar um blob, mensagem ou entidade.

Para tabelas, todas as entidades com o mesmo valor de chave de partição são agrupadas na mesma partição e são armazenadas no mesmo servidor de partição. É importante entender isso ao projetar seu aplicativo. Seu aplicativo deve equilibrar os benefícios de escalabilidade da propagação de entidades por várias partições com as vantagens de acesso de dados do agrupamento de entidades em uma única partição. Uma vantagem importante do agrupamento de entidades em partições é a possibilidade de executar operações atômicas nas entidades da mesma partição, já que uma partição existe em um único servidor.

As partições afetam o balanceamento de carga e a escalabilidade de cada um dos serviços de armazenamento das seguintes formas:

- **Blobs**: a chave de partição de um blob é o nome do contêiner + nome do blob. Isso significa que cada blob possui sua própria partição. Blobs podem, portanto, ser distribuídos em vários servidores a fim de expandir o acesso a eles. Embora os blobs possam ser agrupados logicamente em contêineres de blob, o particionamento não é afetado de forma alguma por esse agrupamento.

- **Mensagens**: a chave de partição de uma mensagem é o nome da fila, portanto, todas mensagens em uma fila são agrupadas em uma única partição e são atendidas por um único servidor. Filas diferentes podem ser processadas por servidores diferentes a fim de equilibrar a carga, não importa a quantidade de filas que uma conta de armazenamento tenha.

- **Entidades**:aA chave de partição de uma entidade é nome da tabela + chave de partição, sendo que a chave de partição é o valor da propriedade obrigatória **PartitionKey** definida pelo usuário para a entidade.

	Entidades dentro de uma tabela podem ter os mesmos valores de chave de partição e, nesse caso, elas são agrupadas na mesma partição. Um aplicativo pode executar transações atômicas em lote nas entidades dentro da mesma partição, já que elas são atendidas pelo mesmo servidor.

	Entidades que estão na mesma tabela, mas que pertencem a partições diferentes podem ter suas cargas equilibradas em servidores diferentes, possibilitando uma tabela maior com escalabilidade superior.

## Consulte também

- [Detalhes de preços de armazenamento](http://azure.microsoft.com/pricing/details/storage/)
- [Armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com consistência sólida](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)


<!--HONumber=54--> 