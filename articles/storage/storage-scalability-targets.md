<properties 
   pageTitle="Metas de desempenho e escalabilidade do Armazenamento do Azure | Microsoft Azure"
   description="Saiba mais sobre as metas de desempenho e escalabilidade para Armazenamento do Azure, incluindo a capacidade, taxa de solicitação e largura de banda de entrada e saída, tanto para contas de armazenamento standard quanto premium. Entender as metas de desempenho para partições em de cada um dos serviços do Armazenamento do Azure."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="08/07/2015"
   ms.author="tamram" />

# Metas de desempenho e escalabilidade do Armazenamento do Azure

Este tópico descreve os tópicos de desempenho e escalabilidade do Armazenamento do Microsoft Azure. Para obter um resumo de outros limites do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

>[AZURE.NOTE]Todas as contas de armazenamento são executadas na nova topologia de rede simples e dão suporte às metas de escalabilidade e desempenho descritas abaixo, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, consulte [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

<!-- -->

>[AZURE.IMPORTANT]As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo. Teste o serviço para determinar se o desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.

>Quando seu aplicativo atingir o limite de uma partição, o Armazenamento do Azure passará a retornar respostas com o código de erro 503 (servidor ocupado) ou o código de erro 500 (tempo limite da operação). Quando isso ocorre, o aplicativo deve usar uma política de retirada exponencial para repetições. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.

Se as necessidades de seu aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e faça o particionamento dos seus objetos de dados nessas contas de armazenamento. Consulte [Detalhes de preços de armazenamento](http://azure.microsoft.com/pricing/details/storage/) para obter informações sobre o preço por volume.

## Metas de escalabilidade para contas de armazenamento padrão

[AZURE.INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

## Metas de escalabilidade para Contas de armazenamento Premium

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../../includes/azure-storage-limits-premium-storage.md)]

## Limites de Armazenamento – Gerenciador de Recursos do Azure

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## Partições no Armazenamento do Azure

Cada objeto com dados armazenado no Armazenamento do Azure (blobs, mensagens, entidades e arquivos) pertence a uma partição e é identificado por uma chave de partição. A partição determina como o Armazenamento do Azure equilibra as cargas de blobs, mensagens, entidades e arquivos em servidores a fim de atender às necessidades de tráfego desses objetos. A chave de partição é exclusiva dentro da conta de armazenamento e é usada para localizar um blob, mensagem ou entidade.

A tabela exibida acima, em [Metas de escalabilidade para contas de armazenamento padrão](#scalability-targets-for-standard-storage-accounts), lista as metas de desempenho para uma única partição para cada serviço.

As partições afetam o balanceamento de carga e a escalabilidade de cada um dos serviços de armazenamento das seguintes formas:

- **Blobs**: a chave de partição de um blob é o nome do contêiner + nome do blob. Isso significa que cada blob possui sua própria partição. Blobs podem, portanto, ser distribuídos em vários servidores a fim de expandir o acesso a eles. Embora os blobs possam ser agrupados logicamente em contêineres de blob, o particionamento não é afetado de forma alguma por esse agrupamento.

- **Arquivos**: a chave da partição de um arquivo é o nome da conta + nome do compartilhamento de arquivo. Isso significa que todos os arquivos em um compartilhamento de arquivos também estão em uma única partição.

- **Mensagens**: a chave de partição de uma mensagem é o nome da fila, portanto, todas mensagens em uma fila são agrupadas em uma única partição e são atendidas por um único servidor. Filas diferentes podem ser processadas por servidores diferentes a fim de equilibrar a carga, não importa a quantidade de filas que uma conta de armazenamento tenha.

- **Entidades**:aA chave de partição de uma entidade é nome da tabela + chave de partição, sendo que a chave de partição é o valor da propriedade obrigatória **PartitionKey** definida pelo usuário para a entidade.

	Todas as entidades com o mesmo valor de chave de partição são agrupadas na mesma partição e são armazenadas no mesmo servidor de partição. É importante entender isso ao projetar seu aplicativo. Seu aplicativo deve equilibrar os benefícios de escalabilidade da propagação de entidades por várias partições com as vantagens de acesso de dados do agrupamento de entidades em uma única partição.

	Uma vantagem importante de agrupar em uma única partição um conjunto de entidades contidas em uma tabela é a possibilidade de executar operações de lote atômicas nas entidades da mesma partição, já que uma partição existe em um único servidor. Portanto, se você quiser executar operações em lote, considere agrupar entidades com a mesma chave de partição.

	Por outro lado, entidades que estão na mesma tabela, mas que pertencem a partições diferentes podem ter suas cargas equilibradas em servidores diferentes, possibilitando uma tabela maior com escalabilidade superior.

## Consulte também

- [Detalhes de preços de armazenamento](http://azure.microsoft.com/pricing/details/storage/)
- [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
- [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage-preview-portal/)
- [Replicação de armazenamento do Azure](storage-redundancy.md)
- [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](storage-performance-checklist.md)
- [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)



 

<!---HONumber=August15_HO7-->