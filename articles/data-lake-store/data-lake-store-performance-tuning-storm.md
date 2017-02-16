---
title: Diretrizes de ajuste de desempenho para Storm do Azure Data Lake Store | Microsoft Docs
description: Diretrizes de ajuste de desempenho para Storm do Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: ebf876f946eceddce9c8c990d8b28fcb969bec23
ms.openlocfilehash: 112226028c053cc91f9fb2bc0e5978f7cb2343ed


---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Diretrizes de ajuste do desempenho para Storm no HDInsight e Azure Data Lake Store

Existem alguns fatores que precisam ser considerados ao ajustar o desempenho de uma topologia Storm.  É importante compreender as características do trabalho feito pelos spouts e bolts (se o trabalho está com uso intensivo de memória ou de E/S).

## <a name="prerequisites"></a>Pré-requisitos 

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md) 
* **Cluster HDInsight do Azure** com acesso a uma conta do Repositório Data Lake. Confira [Criar um cluster HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster. 
* **Executando o cluster Storm no Azure Data Lake Store**.  Para obter mais informações, consulte [Storm no HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview) 
* **Diretrizes de ajuste de desempenho no ADLS**.  Para ver os conceitos gerais de desempenho, confira [Diretrizes de ajuste de desempenho do Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

**Ajustando o Paralelismo da topologia**

É possível obter um melhor desempenho aumentando a simultaneidade de E/S de e para o Azure Data Lake Store.  
Uma topologia Storm tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalho: os trabalhos são distribuídos uniformemente entre as VMs.
* Número de instâncias de executor de spout
* Número de instâncias de executor de bolt
* Número de tarefas de spout
* Número de tarefas de bolt

Por exemplo, em um cluster com quatro VMs e quatro processos de trabalho, 32 executores de spout e 32 tarefas de spout, 256 executores de bolt e 512 tarefas de bolt:

Cada supervisor, que é um nó de trabalho, terá um único processo de trabalho JVM que gerenciará quatro threads de spout e 64 threads de bolt. Em cada thread, as tarefas são executadas sequencialmente. Com a configuração acima, cada thread de spout terá uma tarefa e cada thread de bolt terá duas tarefas.

No Storm, aqui estão os diversos componentes envolvidos e como eles afetam o nível de paralelismo que você tem:
* O nó principal (chamado Nimbus no Storm) é usado para enviar e gerenciar trabalhos. Esses nós não têm impacto sobre o grau de paralelismo.
* Os nós de supervisor – no Azure HDInsight isso corresponde a uma VM do Azure de nó de trabalho.
* As tarefas de trabalho são processos Storm em execução nas VMs. Cada tarefa de trabalho corresponde a uma instância java da JVM. O Storm distribui o número de processos de trabalho que você especificar para os nós de trabalho tão uniformemente quanto possível.
* Instâncias de executor de spout e bolt: cada instância de executor corresponde a um thread em execução dentro dos trabalhos (JVMs)
* Tarefas do Storm: estas são tarefas lógicas que cada um desses threads executa. Isso não altera o nível de paralelismo, então você deve avaliar se precisa de várias tarefas por executor ou não.

## <a name="guidance"></a>Diretrizes

Ao trabalhar com o Azure Data Lake, você obterá melhor desempenho se fizer o seguinte:
* Una seus pequenos anexos em tamanhos maiores (o ideal é 4 MB de tamanho)
* Faça o máximo possível de solicitações simultâneas. Uma vez que cada thread de bolt está fazendo leituras de bloqueio, você deseja ter entre 8 e 12 threads por núcleo para poder manter a NIC e a CPU bem utilizadas.  Uma VM maior permitirá mais solicitações simultâneas.  

## <a name="example"></a>Exemplo

Suponhamos que você tenha um cluster de oito nós de trabalho com a VM D13v2 do Azure.  Uma VM D13v2 tem oito núcleos, portanto, entre os oito nós de trabalho, você tem 64 núcleos no total.

Digamos que façamos oito threads de bolt por núcleo. Dados 64 núcleos, isso significa que desejamos um total de 512 instâncias de executor de bolt (ou seja, threads). Nesse caso, digamos que podemos começar com uma JVM por VM e usar principalmente a simultaneidade de threads na JVM para obter a simultaneidade. Isso significa que precisamos de oito tarefas de trabalho (uma por VM do Azure) e 512 executores de bolt. Dada essa configuração, o Storm tenta distribuir os trabalhos uniformemente entre os nós de trabalho (também conhecidos como nós de supervisor), dando a cada nó de trabalho uma JVM. Agora dentro dos supervisores, o Storm tentará distribuir os executores uniformemente entre os supervisores, dando a cada supervisor (ou seja, JVM) oito threads.

## <a name="further-tuning"></a>Ajuste adicional
Uma vez que você tenha a topologia básica, você pode considerar se deseja ajustar qualquer um dos parâmetros:
* **Número de JVMs por nó de trabalho:** se você tem uma estrutura de dados grande (por exemplo, uma tabela de pesquisa) que você hospeda na memória, então cada JVM exige uma cópia separada, enquanto ter menos JVMs permite que você use a estrutura entre vários threads. Para E/S do bolt, o número de JVMs não faz tanta diferença quanto o número de threads adicionados entre essas JVMs. Para manter a simplicidade, é recomendável uma JVM por trabalho; no entanto, dependendo do que o seu bolt está fazendo ou de que processamento de aplicativo você requer, talvez seja necessário avaliar se este é um número que você precisa ajustar.
* **Número de executores de spout:** já que este exemplo usa bolts para gravar no Azure Data Lake, o número de spouts não é diretamente relevante para o desempenho do bolt. No entanto, dependendo da quantidade de processamento ou E/S ocorrendo no spout, você desejará ajustar os spouts para melhor desempenho. No mínimo, você desejará certificar-se de que tem spouts suficientes para ser capaz de manter os bolts ocupados – ou seja, as taxas de saída dos spouts devem corresponder à taxa de transferência dos bolts. A configuração real dependerá do spout e está fora do escopo deste documento.
* **Número de tarefas:** cada bolt é executado como um único thread. Tarefas adicionais por bolt não fornecem nenhuma simultaneidade adicional. A situação em que elas são benéficas é se o processo de confirmação da tupla utilizar uma grande proporção de seu tempo de execução de bolt. É recomendável agrupar várias tuplas em um anexo maior antes de enviar uma confirmação do bolt, de modo que, na maioria dos casos, várias tarefas não fornecem nenhum benefício adicional.
* **Agrupamento de ordem aleatória ou local:** quando essa configuração é habilitada, tuplas são enviadas a bolts dentro do mesmo processo de trabalho. Isso reduz as chamadas de rede e comunicação entre processos. Isso é recomendado para a maioria das topologias.

Como uma abordagem inicial, é recomendável começar com o cenário básico e testar com seus próprios dados para ajustar os parâmetros mencionados acima, de modo a obter o desempenho ideal.

## <a name="tuning-the-spout"></a>Ajustar o spout

**Tempo limite de tupla**

topology.message.timeout.secs – essa configuração determina a quantidade de tempo que uma mensagem para concluir e receber a confirmação antes de ser considerada com falha.

**Memória máxima por processo de trabalho**

Worker.childopts – essa configuração permite especificar parâmetros de linha de comando adicionais para os trabalhos java. A configuração mais comumente usada aqui é XmX, que determina o máximo de memória alocado para o heap de uma JVM.

**Máx. de spouts pendentes**

Topology.max.spout.pending – esta configuração determina o número de tuplas que podem estar em voo (ainda não confirmadas em todos os nós na topologia) por thread de spout em qualquer ponto no tempo.

Um bom cálculo a fazer é a estimativa de tamanho de cada uma de suas tuplas. Em seguida, calcule quanta memória um thread de spout tem. A memória total alocada para um thread dividida por esse valor deve fornecer o limite superior para o parâmetro de spout máx. pendentes.

## <a name="tuning-the-bolt"></a>Ajustar o Bolt
Ao gravar para ADLS, é recomendável definir uma política de sincronização de tamanho (buffer no lado do cliente) para 4 MiB.  Uma liberação ou um hsync() é então executado somente quando o tamanho do buffer é o valor acima.  O driver do ADLS na VM de trabalho faz esse buffer automaticamente, a menos que o usuário execute explicitamente um hsync().

O bolt do Storm ADLS padrão tem um parâmetro de política de sincronização de tamanho (fileBufferSize) que pode ser usado para ajustar esse parâmetro.

Em topologias com uso intensivo de E/S, é recomendável que cada thread de bolt grave seu próprio arquivo e defina uma política de rotação de arquivo (fileRotationSize).  Quando o arquivo atingir um determinado tamanho, o fluxo será automaticamente liberado e a gravação ocorrerá em um novo arquivo.  O tamanho do arquivo recomendado para rotação é de 1 GB.

**Quando confirmar:** no Storm, um spout permanece com uma tupla até que ela seja explicitamente confirmada pelo bolt.  Se uma tupla foi lida pelo bolt mas ainda não foi confirmada, isso significa que não é garantido que o bolt tenha persistido no back-end do Azure Data Lake Store.  Após uma tupla ser confirmada, é possível garantir a persistência do spout pelo bolt, de modo que ele pode então excluir os dados de origem de qualquer fonte da qual esteja lendo.  

**Para melhor desempenho em ADLS:** recomendamos que o bolt armazene em buffer 4 MB de dados da tupla e, em seguida, grave-os no back-end do ADLS como uma gravação de 4 MB. Depois que os dados tiverem sido gravados com êxito no repositório (chamando hflush()), o bolt poderá então confirmar os dados de volta para o spout. É isso que o bolt de exemplo fornecido aqui faz. Também é aceitável manter um número maior de tuplas antes da chamada de hflush() ser feita e das tuplas serem confirmadas. No entanto, isso aumenta o número de tuplas em voo que o spout precisa armazenar e, portanto, aumenta a quantidade de memória exigida por JVM.

Determinados aplicativos podem ter um requisito de confirmar mais frequentemente (em tamanhos de dados menores que 4 MB) por outros motivos não relacionados a desempenho. No entanto, isso pode afetar a taxa de transferência de E/S para o back-end de armazenamento, então o cliente deve avaliar com cuidado essa compensação em relação ao desempenho de E/S do bolt.

Se a taxa de entrada de tuplas não é alta suficiente de modo que o buffer de 4 MB leva muito tempo para ser preenchido, convém considerar sua redução de várias formas:
* Reduzir o número de bolts, de modo que haja menos buffers de 4 MB para preencher
* Ter uma política baseada em tempo ou em contagem, na qual um hflush() é disparado a cada x liberações ou a cada y milissegundos e as tuplas acumuladas até esse ponto são confirmadas de volta.

Observe que neste caso a taxa de transferência é menor, mas de todo modo, com uma taxa de eventos baixa, a taxa de transferência máxima não é o objetivo principal.  Essas reduções acima permitem que você reduza o tempo de ponta a ponta que leva para uma tupla fluir até o repositório, o que pode ser importante se você quiser um pipeline em tempo real mesmo diante de uma taxa de eventos baixa.  Observe também que, se a taxa de entrada de tuplas for baixa, você também desejará ajustar o parâmetro topology.message.timeout_secs para que as tuplas não atinjam o tempo limite enquanto estiverem sendo armazenadas em buffer ou processadas.

## <a name="interpreting-storm-ui"></a>Interpretando a interface do usuário do Storm  
Enquanto a topologia está em execução, você pode monitorá-la na interface do usuário do Storm. Ao examinar a interface do usuário, estes são os parâmetros principais a observar:

* **Latência total de execução do processo** – é o tempo médio que uma tupla leva para ser emitida pelo spout, processada pelo bolt e confirmada.

* **Latência total de processo do bolt** – é o tempo médio gasto pela tupla no bolt até receber uma confirmação.

* **Latência total de execução do bolt** – este é o tempo médio gasto pelo bolt no método execute.

* **Número de falhas** – refere-se ao número de tuplas que deixaram de ser completamente processadas antes que atingissem o tempo limite.

* **Capacidade** – uma medida da ocupação do sistema. Se esse número é 1, os bolts estão trabalhando tão rápido quanto possível. Se é menor que 1, aumente o paralelismo. Se é maior, reduza o paralelismo.

## <a name="common-troubleshooting-scenarios"></a>Cenários comuns de solução de problemas
* **Grande número de tuplas atingindo o tempo limite** – procure em cada nó na topologia para determinar onde está o afunilamento. O motivo mais comum para isso é que os bolts não sejam capazes de acompanhar os spouts, fazendo com que as tuplas congestionem os buffers internos enquanto aguardam para serem processadas. Considere aumentar o valor de tempo limite ou diminuir o máx. de spouts pendentes.

* **latência total de execução do processo alta, mas latência de processo do bolt baixa** – nesse caso, uma das tuplas não estão sendo rápido o suficiente. Verifique se há um número suficiente de confirmadores. Outra possibilidade é que estejam aguardando na fila por tempo demais antes que os bolts comecem a processá-las. Diminua o máx. de spouts pendentes.

* **Latência de execute do bolt elevada** – isso significa que o método execute() do seu bolt está demorando muito. Otimize o código ou examine os tamanhos de gravação/comportamento de liberação.

## <a name="limitation"></a>Limitação 
Limitação do ADLS: se os limites de largura de banda fornecidos pelo ADLS fossem atingidos, você começaria a ver falhas de tarefa. Isso poderia ser identificado observando os erros de limitação nos logs de tarefa.  Você pode diminuir o paralelismo aumentando o tamanho do contêiner.  Se precisar de mais simultaneidade para seu trabalho, entre em contato conosco.   
Para verificar se há problemas de limitação, você precisa habilitar o log de depuração no lado do cliente. Veja como fazer isso:

1. Altere o descrito a seguir em Ambari > Storm > Configuração > Avançado storm-worker-log4j.  Altere &lt;root level="info"&gt; para &lt;root level=”debug”&gt;.  Reinicie todos os nós/o serviço para que a configuração entre em vigor.
2. Monitore os logs de topologia Storm em nós de trabalho (em /var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;port&gt;/worker.log) para exceções de limitação de ADLS.

## <a name="additional-tuning"></a>Ajuste Adicional
Ajuste de desempenho adicional para o Storm pode ser referenciado neste [blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

## <a name="examples-to-run"></a>Exemplos para execução
Experimente este exemplo localizado no [github](https://github.com/hdinsight/storm-performance-automation).



<!--HONumber=Jan17_HO2-->


