<properties 
   pageTitle="Filas do Azure e filas do Barramento de Serviço — comparações e contrastes"
   description="Analisa diferenças e semelhanças entre dois tipos de fila oferecidos pelo Azure."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sethm" />

# Filas do Azure e filas do Barramento de Serviço — comparações e contrastes

Este artigo analisa as diferenças e semelhanças entre os dois tipos de fila oferecidos pelo Microsoft Azure atualmente: Filas do Azure e filas do Barramento de Serviço. Usando essas informações, é possível comparar e contrastar as respectivas tecnologias e tomar uma decisão mais informada sobre qual solução atende melhor às suas necessidades.

## Introdução

O Microsoft Azure oferece suporte a dois tipos de mecanismo de fila: **Filas do Azure** e **Filas do Barramento de Serviço**.

As **Filas do Azure**, que fazem parte da infraestrutura do [armazenamento do Azure](http://azure.microsoft.com/services/storage/), apresentam uma interface Get/Put/Peek baseada em REST, fornecendo sistema de mensagens confiável e persistente dentro e entre serviços.

**As filas do Barramento de Serviço** fazem parte de uma infraestrutura mais ampla de [mensagens do Azure](http://azure.microsoft.com/services/messaging/), que oferece suporte ao enfileiramento, bem como à publicação/assinatura, comunicação remota do serviço Web e padrões de integração. Para obter mais informações sobre filas, tópicos/assinaturas e retransmissões do Barramento de Serviço, consulte [Visão geral dos padrões de sistema de mensagens do Barramento de Serviço](https://msdn.microsoft.com/library/hh410103.aspx).

Embora ambas as tecnologias de enfileiramento coexistam, as Filas do Azure foram introduzidas primeiro, como um mecanismo de armazenamento de fila dedicado criado com base nos serviços de armazenamento do Azure. As filas do Barramento de Serviço são criadas com base na infraestrutura mais ampla do “sistema de mensagens agenciado”, desenvolvida para integrar aplicativos ou componentes de aplicativo que podem incluir vários protocolos de comunicação, contratos de dados, domínios confiáveis e/ou ambientes de rede.

Este artigo compara as duas tecnologias de filas oferecidas pelo Azure, abordando as diferenças no comportamento e na implementação dos recursos fornecidos por cada uma. O artigo também fornece diretrizes para escolher quais recursos são mais adequados às suas necessidades de desenvolvimento de aplicativo.

## Considerações de seleção da tecnologia

As Filas do Azure e as filas do Barramento de Serviço são implementações do serviço de enfileiramento de mensagens atualmente oferecido no Microsoft Azure. Cada uma tem um conjunto de recursos ligeiramente diferente, o que significa que você pode escolher uma ou outra, ou usar ambas, dependendo das necessidades da sua solução específica ou do problema comercial/técnico que está resolvendo.

Ao determinar qual tecnologia de enfileiramento é adequada ao propósito de uma determinada solução, os desenvolvedores e arquitetos de soluções devem considerar as recomendações abaixo. Para obter mais detalhes, consulte a próxima seção.

Como um arquiteto/desenvolvedor de soluções, **você deve considerar o uso das Filas do Azure** quando:

- Seu aplicativo precisar armazenar mais de 80 GB de mensagens em uma fila, onde as mensagens têm um tempo de vida de menos de 7 dias.

- Seu aplicativo desejar rastrear o progresso do processamento de uma mensagem na fila. Isso será útil se o trabalho de processamento de uma mensagem falhar. Um trabalho subsequente pode usar essas informações para continuar de onde o anterior parou.

- Você precisar de logs do servidor de todas as transações executadas em suas filas.

Como um arquiteto/desenvolvedor de soluções, **você deve considerar o uso das filas do Barramento de Serviço** quando:

- Sua solução precisar ser capaz de receber mensagens sem precisar sondar a fila. Com o Barramento de Serviço, isso pode ser alcançado pela operação de recebimento de sondagem longa usando os protocolos baseados em TCP que têm suporte do Barramento de Serviço.

- Sua solução exigir que a fila forneça uma entrega ordenada PEPS (primeiro a entrar, primeiro a sair).

- Você desejar uma experiência simétrica no Azure e no Windows Server (nuvem privada). Para obter mais informações, consulte [Barramento de Serviço para Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- Sua solução precisar ser capaz de oferecer suporte à detecção automática de duplicidades.

- Você desejar que seu aplicativo processe mensagens como fluxos paralelos de longa execução (mensagens associadas a um fluxo usando a propriedade [SessionId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) na mensagem). Nesse modelo, cada nó no aplicativo de consumo compete por fluxos, em oposição às mensagens. Quando um fluxo é fornecido a um nó de consumo, o nó pode examinar o estado do fluxo do aplicativo usando transações.

- Sua solução exigir comportamento transacional e atomicidade ao enviar ou receber várias mensagens de uma fila.

- A característica de TTL (vida útil) da carga de trabalho específica de aplicativo puder exceder o período de 7 dias.

- Seu aplicativo tratar mensagens que podem exceder 64 KB, mas provavelmente não se aproximarão do limite de 256 KB.

- Você tiver que lidar com a necessidade de fornecer um modelo de acesso baseado em função às filas e diferentes direitos/permissões para remetentes e destinatários.

- O tamanho da fila não for exceder 80 GB.

- Você desejar usar o agente do sistema de mensagens baseado em padrões AMQP 1.0. Para obter mais informações sobre AMQP, consulte [Visão geral do AMQP do Barramento de Serviço](https://msdn.microsoft.com/library/jj841072.aspx).

- Você puder prever uma migração final da comunicação ponto a ponto baseada em fila para um padrão de troca de mensagens que permita a integração perfeita de receptores (assinantes) adicionais, cada um dos quais recebe cópias independentes de algumas ou todas as mensagens enviadas à fila. O último se refere ao recurso de publicação/assinatura fornecido originalmente pelo Barramento de Serviço.

- Sua solução de sistema de mensagens tiver que oferecer suporte à garantia de entrega “No máximo uma vez”, sem a necessidade de criar componentes de infraestrutura adicional.

- Você desejar publicar e consumir lotes de mensagens.

- Você exigir integração total à pilha de comunicação WCF (Windows Communication Foundation) no .NET Framework.

## Comparando as Filas do Azure e as filas do Barramento de Serviço

As tabelas nas seções a seguir fornecem um agrupamento lógico de recursos de fila e permitem que você compare, rapidamente, os recursos disponíveis nas Filas do Azure e nas filas do Barramento de Serviço.

## Recursos básicos

Esta seção compara alguns dos recursos básicos de enfileiramento fornecidos pelas Filas do Azure e filas do Barramento de Serviço.

|Critérios de comparação|Filas do Azure|Filas de barramento de serviço|
|---|---|---|
|Garantia de ordenação|**Não** <br/><br>Para obter mais informações, consulte a primeira observação na seção “Informações adicionais”.</br>|**Sim — PEPS (primeiro a entrar, primeiro a sair)**<br/><br>(pelo uso de sessões de mensagens)|
|Garantia de entrega|**Pelo menos uma vez**|**Pelo menos uma vez**<br/><br/>**No máximo uma vez**|
|Suporte a transações|**Não**|**Sim**<br/><br/>(pelo uso de transações locais)|
|Comportamento de recebimento|**Sem bloqueio**<br/><br/>(concluído imediatamente se nenhuma mensagem nova for encontrada)|**Bloqueio com/sem tempo limite**<br/><br/>(oferece sondagem longa ou a ["técnica Comet"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Sem bloqueio**<br/><br/>(apenas pelo uso da API gerenciada pelo .NET)|
|API de estilo push|**Não**|**Sim**<br/><br/>API .NET [OnMessage](https://msdn.microsoft.com/library/jj908682.aspx) e [sessões OnMessage](https://msdn.microsoft.com/library/dn790528.aspx).|
|Modo de recebimento|**Inspecionar e Conceder**|**Inspecionar e Bloquear**<br/><br/>**Receber & Excluir**|
|Modo de acesso exclusivo|**Com base em concessão**|**Com base em bloqueio**|
|Duração da concessão/do bloqueio|**30 segundos (padrão)**<br/><br/>**7 dias (máximo)** (Você pode renovar ou liberar uma concessão de mensagem usando a API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx).)|**60 segundos (padrão)**<br/><br/>Você pode renovar um bloqueio de mensagem usando a API [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).|
|Precisão da concessão/do bloqueio|**Nível da mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo limite diferente, que pode ser atualizado conforme a necessidade durante o processamento da mensagem, usando a API [UpdateMessage](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx))|**Nível da fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas você pode renovar o bloqueio usando a API [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).)|
|Recebimento em lote|**Sim**<br/><br/>(especificando explicitamente a contagem de mensagens ao recuperar mensagens, até um máximo de 32 mensagens)|**Sim**<br/><br/>(habilitando implicitamente uma propriedade de pré-busca ou explicitamente pelo uso de transações)|
|Envio em lote|**Não**|**Sim**<br/><br/>(usando transações ou envio em lote no lado do cliente)|

### Informações adicionais

- Geralmente, as mensagens nas Filas do Azure são do tipo PEPS, mas, às vezes, elas podem estar fora de ordem; por exemplo, quando a duração do tempo limite da visibilidade de uma mensagem expirar (por exemplo, como resultado de um travamento do aplicativo cliente durante o processamento). Quando o tempo limite da visibilidade se esgotar, a mensagem se tornará visível novamente na fila de outro trabalho para que seja removida da fila. Nesse ponto, a mensagem recentemente visível pode ser colocada na fila (para ser removida da fila novamente) após uma mensagem que foi originalmente enfileirada depois dela.

- Se você já está usando Blobs ou Tabelas do Armazenamento do Azure e começar a usar filas, haverá garantia de 99,9% de disponibilidade. Se você usar Blobs ou Tabelas com filas do Barramento de Serviço, a disponibilidade será menor.

- O padrão PEPS garantido nas filas do Barramento de Serviço exige o uso de sessões de mensagens. Se o aplicativo travar durante o processamento de uma mensagem recebida no modo **Inspecionar e Bloquear**, na próxima vez que um destinatário da fila aceitar uma sessão de mensagens, ele começará com a mensagem com falha após a expiração do seu período TTL (vida útil).

- As Filas do Azure foram projetadas para oferecer suporte a cenários de enfileiramento padrão, como desassociação dos componentes de aplicativo para aumentar a escalabilidade e a tolerância para falhas, redistribuição de carga e criação de fluxos de trabalho do processo.

- As filas do Barramento de Serviço oferecem suporte à garantia de entrega *Pelo menos uma vez*. Além disso, a semântica *No máximo uma vez* pode ter suporte usando o estado da sessão para armazenar o estado do aplicativo e usando transações para receber mensagens automaticamente e atualizar o estado da sessão. O Serviço do Fluxo de Trabalho do Azure usa essa técnica para garantir a entrega No máximo uma vez.

- As Filas do Azure fornecem um modelo de programação uniforme e consistente entre filas, tabelas e BLOBs — para desenvolvedores e equipes de operações.

- As filas do Barramento de Serviço fornecem suporte para transações locais no contexto de uma fila única.

- O modo *Receber e Excluir* com suporte do Barramento de Serviço tem a capacidade de reduzir a contagem de operações de mensagens (e custos associados) em troca da garantia de entrega reduzida.

- As Filas do Azure fornecem concessões com a capacidade de estender as concessões para mensagens. Isso permite que os trabalhos mantenham concessões curtas nas mensagens. Desse modo, se um trabalho travar, a mensagem poderá ser processada rapidamente de novo por outro trabalho. Além disso, um trabalho poderá estender a concessão em uma mensagem se precisar processá-la por mais tempo do que o tempo de concessão atual.

- As Filas do Azure oferecem um tempo limite de visibilidade que você pode definir no enfileiramento de uma mensagem ou na remoção dela da fila. Além disso, é possível atualizar uma mensagem com diferentes valores no tempo de execução, bem como atualizar diferentes valores entre mensagens na mesma fila. Os tempos limite de bloqueio do Barramento de Serviço são definidos nos metadados da fila. No entanto, é possível renovar o bloqueio chamando o método [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).

- O tempo limite máximo para um bloqueio de operação de recebimento nas filas do Barramento de Serviço é de 24 dias. No entanto, os tempos limite baseados em REST têm um valor máximo de 55 segundos.

- O envio em lote do lado do cliente fornecido pelo Barramento de Serviço permite que um cliente da fila coloque em lote várias mensagens em uma única operação de envio. O envio em lote está disponível apenas para operações de envio assíncronas.

- Recursos como o limite máximo de 200 TB das Filas do Azure (mais quando você virtualiza contas) e filas ilimitadas a tornam uma plataforma ideal para provedores de SaaS.

- As Filas do Azure fornecem um mecanismo de controle de acesso flexível e delegado ao desempenho.

## Recursos avançados

Esta seção compara recursos avançados fornecidos pelas Filas do Azure e filas do Barramento de Serviço.

|Critérios de comparação|Filas do Azure|Filas de barramento de serviço|
|---|---|---|
|Entrega agendada|**Sim**|**Sim**|
|Mensagens mortas automáticas|**Não**|**Sim**|
|Aumentando o valor da vida útil da fila|**Sim**<br/><br/>(por meio da atualização in-loco do tempo limite de visibilidade)|**Sim**<br/><br/>(fornecido por uma função de API dedicada)|
|Suporte a mensagens suspeitas|**Sim**|**Sim**|
|Atualização in-loco|**Sim**|**Sim**|
|Log de transações do servidor|**Sim**|**Não**|
|Métricas de armazenamento|**Sim**<br/><br/>**Métricas de Minuto**: fornecem métricas em tempo real para disponibilidade, TPS, contagens de chamada de API, contagens de erros e muito mais, tudo em tempo real (agregados por minuto e relatados em poucos minutos a partir do que acabou de acontecer em produção). Para obter mais informações, consulte [Sobre as Métricas de Analítica de Armazenamento](https://msdn.microsoft.com/library/hh343258.aspx).|**Sim**<br/><br/>(consultas em massa chamando [GetQueues](https://msdn.microsoft.com/library/hh293128.aspx))|
|Gerenciamento de estado|**Não**|**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx)|
|Encaminhamento automático de mensagem|**Não**|**Sim**|
|Função Limpar fila|**Sim**|**Não**|
|Grupos de mensagens|**Não**|**Sim**<br/><br/>(pelo uso de sessões de sistema de mensagens)|
|Estado do aplicativo por grupo de mensagens|**Não**|**Sim**|
|Detecção de duplicidade|**Não**|**Sim**<br/><br/>(configurável no lado do remetente)|
|Integração do WCF|**Não**|**Sim**<br/><br/>(oferece associações do WCF prontas para uso)|
|Integração do WF|**Personalizada**<br/><br/>(exige a criação de uma atividade do WF personalizada)|**Nativa**<br/><br/>(oferece atividades do WF prontas para uso)|
|Procurando grupos de mensagens|**Não**|**Sim**|
|Buscando sessões de mensagem por ID|**Não**|**Sim**|

### Informações adicionais

- Ambas as tecnologias de enfileiramento permitem que uma mensagem seja agendada para entrega em um momento posterior.

- O encaminhamento automático da fila permite que milhares de filas encaminhem automaticamente suas mensagens para uma única fila, na qual o aplicativo de recebimento consome a mensagem. É possível usar esse mecanismo para fins de segurança, controlar o fluxo e isolar o armazenamento entre cada editor de mensagem.

- As Filas do Azure oferecem suporte à atualização do conteúdo da mensagem. Você pode usar essa funcionalidade para informações de estado persistentes e atualizações incrementais de progresso na mensagem para que ela possa ser processada a partir do último ponto de verificação conhecido, em vez de começar do zero. Com filas do Barramento de Serviço, você pode habilitar o mesmo cenário usando as sessões de mensagens. As sessões permitem salvar e recuperar o estado de processamento do aplicativo (usando [SetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- As mensagens mortas, que têm suporte apenas nas filas do Barramento de Serviço, podem ser úteis para isolar mensagens que não podem ser processadas com êxito pelo aplicativo de recebimento ou quando as mensagens não podem atingir seu destino devido a uma propriedade de TTL expirada. O valor de TTL especifica quanto tempo uma mensagem permanece na fila. Com o Barramento de Serviço, a mensagem será movida para uma fila especial chamada $DeadLetterQueue quando o período TTL expirar.

- Para localizar mensagens "suspeitas" nas Filas do Azure, ao remover uma mensagem, o aplicativo examina a propriedade DequeueCount da mensagem. Se DequeueCount estiver acima de um determinado limite, o aplicativo moverá a mensagem para uma fila de “mensagens mortas” definida pelo aplicativo.

- As Filas do Azure permitem que você obtenha um log detalhado de todas as transações executadas na fila, bem como as métricas agregadas. Essas duas opções são úteis para depurar e entender como o aplicativo usa as Filas do Azure. Elas também são úteis para o ajuste de desempenho do aplicativo e redução dos custos do uso de filas.

- O conceito de "sessões de mensagens" com suporte do Barramento de Serviço permite que as mensagens que pertencem a um determinado grupo lógico sejam associadas a um destinatário específico que, por sua vez, cria uma afinidade de sessão entre as mensagens e seus respectivos destinatários. Você pode habilitar essa funcionalidade avançada no Barramento de Serviço definindo a propriedade [SessionID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) em uma mensagem. Os receptores podem escutar em uma ID de sessão específica e receber mensagens que compartilham o identificador de sessão especificado.

- A funcionalidade de detecção de duplicidade com suporte das filas do Barramento de Serviço remove automaticamente mensagens duplicadas enviadas a uma fila ou um tópico, com base no valor da propriedade [MessageID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx).

## Capacidade e cotas

Esta seção compara as Filas do Azure e as filas do Barramento de Serviço da perspectiva de capacidade e cotas que podem ser aplicáveis.

|Critérios de comparação|Filas do Azure|Filas de barramento de serviço|
|---|---|---|
|Tamanho máximo da fila|**200 TB**<br/><br/>(limitado a uma capacidade de conta de armazenamento única)|**1 GB a 80 GB**<br/><br/>(definido na criação de uma fila e [habilitando particionamento](https://msdn.microsoft.com/library/dn520246.aspx) – consulte a seção "Informações adicionais")|
|Tamanho máximo da mensagem|**64 KB**<br/><br/>(48 KB ao usar codificação **Base64**)<br/><br/>O Azure oferece suporte a mensagens grandes combinando filas e blobs — nesse ponto, você pode enfileirar até 200 GB para um único item.|**256 KB**<br/><br/>(incluindo cabeçalho e corpo, tamanho máximo do cabeçalho: 64 KB)|
|TTL máxima da mensagem|**7 dias**|**Ilimitada**|
|Número máximo de filas|**Ilimitado**|**10.000**<br/><br/>(por namespace de serviço, pode ser aumentado)|
|Número máximo de clientes simultâneos|**Ilimitado**|**Ilimitado**<br/><br/>(o limite de 100 conexões simultâneas se aplica somente à comunicação baseada no protocolo TCP)|

### Informações adicionais

- O Barramento de Serviço impõe limites de tamanho de fila. O tamanho máximo da fila é especificado na criação da fila e pode ter um valor entre 1 e 80 GB. Se o valor do tamanho da fila definido na criação da fila for atingido, mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para obter mais informações sobre cotas no Barramento de Serviço, consulte [Cotas do Barramento de Serviço](https://msdn.microsoft.com/library/ee732538.aspx).

- Você pode criar filas do Barramento de Serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Com o particionamento habilitado (que é o padrão), o Barramento de Serviço cria 16 partições para cada GB especificado. Assim, se você criar uma fila que tenha 5 GB, com 16 partições, o tamanho máximo da fila será (5 * 16) = 80 GB. É possível ver o tamanho máximo da fila ou do tópico particionado observando sua entrada no portal de gerenciamento do Azure.

- Com as Filas do Azure, se o conteúdo da mensagem não for XML seguro, ele deverá ser codificado em **Base64**. Se você codificar a mensagem em **Base64**, a carga de usuário poderá ser de até 48 KB, em vez de 64 KB.

- Com as filas do Barramento de Serviço, cada mensagem armazenada em uma fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder 256 KB.

- Quando os clientes se comunicam com as filas do Barramento de Serviço pelo protocolo TCP, o número máximo de conexões simultâneas para uma única fila do Barramento de Serviço é limitado a 100. Esse número é compartilhado entre remetentes e receptores. Se essa cota for atingida, as solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Esse limite não é imposto a clientes que se conectam às filas usando a API baseada em REST.

- Se precisar de mais de 10.000 filas em um único namespace do Barramento de Serviço, você poderá entrar em contato com a equipe de suporte do Azure e solicitar um aumento. Para expandir para além de 10.000 filas com Barramento de Serviço, você também pode criar namespaces adicionais usando o portal de gerenciamento do Azure.

## Gerenciamento e operações

Esta seção compara os recursos de gerenciamento fornecidos pelas Filas do Azure e filas do Barramento de Serviço.

|Critérios de comparação|Filas do Azure|Filas de barramento de serviço|
|---|---|---|
|Protocolo de gerenciamento|**REST por HTTP/HTTPS**|**REST por HTTPS**|
|Protocolo de tempo de execução|**REST por HTTP/HTTPS**|**REST por HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP com TLS)**|
|API gerenciada pelo .NET|**Sim**<br/><br/>(API do Cliente de Armazenamento gerenciada pelo .NET)|**Sim**<br/><br/>(API do sistema de mensagens agenciado gerenciada pelo .NET)|
|C++ nativo|**Sim**|**Não**|
|API Java|**Sim**|**Sim**|
|API PHP|**Sim**|**Sim**|
|API Node.js|**Sim**|**Sim**|
|Suporte a metadados arbitrários|**Sim**|**Não**|
|Regras de nomenclatura da fila|**Até 63 caracteres**<br/><br/>(letras em um nome de fila devem estar em minúsculas)|**Até 260 caracteres**<br/><br/>(nomes de fila diferenciam maiúsculas de minúsculas)|
|Função Obter tamanho da fila|**Sim**<br/><br/>(valor aproximado se as mensagens expirarem depois da TTL sem que sejam excluídas)|**Sim**<br/><br/>(valor exato, pontual)|
|Função Inspecionar|**Sim**|**Sim**|

### Informações adicionais

- As Filas do Azure fornecem suporte a atributos arbitrários que podem ser aplicados à descrição da fila, na forma de pares de nome/valor.

- As duas tecnologias oferecem a capacidade de inspecionar uma mensagem sem precisar bloqueá-la, o que pode ser útil ao implementar uma ferramenta de gerenciador/navegador de filas.

- As APIs do sistema de mensagens agenciado .NET do Barramento de Serviço aproveitam as conexões de TCP full duplex para melhor desempenho quando comparadas ao REST por HTTP, além de oferecerem suporte ao protocolo padrão AMQP 1.0.

- Os nomes de fila do Azure podem ter de 3 a 63 caracteres, podem conter letras minúsculas, número e hifens. Para obter mais informações, consulte [Nomeando filas e metadados](https://msdn.microsoft.com/library/dd179349.aspx).

- Os nomes de fila do Barramento de Serviço podem ter até 260 caracteres e têm menos regras restritivas de nomenclatura. Os nomes de fila do Barramento de Serviço podem conter letras, números, pontos (.), hifens (-) e sublinhados (_).

## Desempenho

Esta seção compara as Filas do Azure e as filas do Barramento de Serviço de uma perspectiva de desempenho.

|Critérios de comparação|Filas do Azure|Filas de barramento de serviço|
|---|---|---|
|Taxa de transferência máxima|**Até 2.000 mensagens por segundo**<br/><br/>(com base no parâmetro de comparação com mensagens de 1 KB)|**Até 2.000 mensagens por segundo**<br/><br/>(com base no parâmetro de comparação com mensagens de 1 KB)|
|Latência média|**10 ms**<br/><br/>(com [TCP Nagle](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) desabilitado)|**20 a 25 ms**|
|Comportamento de limitação|**Rejeitar com código HTTP 503**<br/><br/>(solicitações limitadas não são tratadas como faturáveis)|**Rejeitar com exceção/HTTP 503**<br/><br/>(solicitações limitadas não são tratadas como faturáveis)|

### Informações adicionais

- Uma única fila do Azure pode processar até 2.000 transações por segundo. Uma transação é uma operação **Put**, **Get** ou **Delete**. O envio de uma única mensagem a uma fila (**Put**) é contado como uma transação, mas receber uma mensagem, muitas vezes, é um processo de duas etapas que envolve a recuperação (**Get**), seguido por uma solicitação para remover a mensagem da fila (**Delete**). Consequentemente, uma operação de remoção da fila bem-sucedida geralmente envolve duas transações. A recuperação de várias mensagens em um lote pode reduzir o impacto desse processo, pois você pode **obter** até 32 mensagens em uma única transação, seguida pela **exclusão** de cada uma delas. Para obter uma melhor taxa de transferência, é possível criar várias filas (uma conta de armazenamento pode ter um número ilimitado de filas).

- Quando seu aplicativo atinge a taxa de transferência máxima para uma fila do Azure, uma resposta "Servidor HTTP 503 ocupado" é normalmente retornada do serviço de fila. Quando isso ocorre, o aplicativo deve disparar a lógica de repetição com atraso de retirada exponencial.

- A latência das Filas do Azure é de 10 milissegundos em média no tratamento de mensagens pequenas (menos de 10 KB) de um serviço hospedado que esteja no mesmo local (região) da conta de armazenamento.

- As Filas do Azure e as filas do Barramento de Serviço impõem comportamento de limitação rejeitando solicitações a uma fila que está sendo limitada. No entanto, nenhuma delas trata solicitações limitadas como faturáveis.

- Os parâmetros de comparação em filas do Barramento de Serviço demonstraram que uma única fila pode atingir uma taxa de transferência de até 2.000 mensagens por segundo com um tamanho de aproximadamente 1 KB. Para atingir uma taxa de transferência mais alta, use várias filas. Para obter mais informações sobre otimização de desempenho com Barramento de Serviço, consulte [Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço](https://msdn.microsoft.com/library/hh528527.aspx).

- Quando a taxa de transferência máxima é atingida por uma fila do Barramento de Serviço, uma resposta [ServerBusyException](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) (ao usar a API do sistema de mensagens agenciado .NET) ou HTTP 503 (ao usar a API baseada em REST) será retornada ao cliente da fila, indicando que a fila está sendo limitada.

## Autenticação e autorização

Esta seção aborda os recursos de autenticação e autorização com suporte nas Filas do Azure e filas do Barramento de Serviço.

|Critérios de comparação|Filas do Azure|Filas de barramento de serviço|
|---|---|---|
|Autenticação|**Chave simétrica**|**Chave simétrica**|
|Modelo de controle de acesso|Acesso delegado via tokens de SAS.|RBAC por meio de ACS|
|Federação do provedor de identidade|**Não**|**Sim**|

### Informações adicionais

- Cada solicitação de ambas as tecnologias de enfileiramento deve ser autenticada. Não há suporte para filas públicas com acesso anônimo. Usando SAS, você pode tratar esse cenário publicando uma SAS somente gravação, SAS somente leitura ou até mesmo uma SAS de acesso completo.

- O esquema de autenticação fornecido pelas Filas do Azure envolve o uso de uma chave simétrica, que é um HMAC (Message Authentication Code) baseado em hash, calculado com o algoritmo SHA-256 e codificado como uma cadeia de caracteres **Base64**. Para obter mais informações sobre o respectivo protocolo, consulte [Autenticando o acesso à sua conta de armazenamento](https://msdn.microsoft.com/library/hh225339.aspx). As filas do Barramento de Serviço oferecem suporte a um modelo semelhante usando chaves simétricas. Para obter mais informações, consulte [Autenticação de Assinatura de Acesso Compartilhado com Barramento de Serviço](https://msdn.microsoft.com/library/dn170477.aspx).

- O Controle de Acesso do Active Directory do Microsoft Azure (também conhecido como Serviço de Controle de Acesso ou ACS) com suporte do Barramento de Serviço oferece três funções distintas: **Admin**, **Remetente** e **Receptor**, que não tem suporte no momento das Filas do Azure.

- Como o Barramento de Serviço oferece integração do ACS, ele permite a federação com o Active Directory (usando ADFS) e outros provedores de identidade comuns da Web.

## Custo

Esta seção compara as Filas do Azure e as filas do Barramento de Serviço de uma perspectiva de custo.

|Critérios de comparação|Filas do Azure|Filas de barramento de serviço|
|---|---|---|
|Custo de transações da fila|**US$ 0,0005**<br/><br/>(por 10.000 transações)|**Camada básica**: **US$ 0,05**<br/><br/>(por milhões de operações)|
|Operações faturáveis|**Todas**|**Apenas Enviar/Receber**<br/><br/>(sem cobrança para outras operações)|
|Transações ociosas|**Faturáveis**<br/><br/>(a consulta de uma fila vazia é contada como transação faturável)|**Faturáveis**<br/><br/>(um recebimento em uma fila vazia é considerado uma mensagem faturável)|
|Custo de armazenamento|**US$ 0,07**<br/><br/>(por GB/mês)|**US$ 0,00**|
|Custos de transferência de dados de saída|**US$ 0,12 a US$ 0,19**<br/><br/>(dependendo da localização geográfica)|**US$ 0,12 a US$ 0,19**<br/><br/>(dependendo da localização geográfica)|

### Informações adicionais

- As transferências de dados são cobradas com base no volume total de dados que deixam os datacenters do Azure pela Internet em um determinado período de cobrança.

- As transferências de dados entre os serviços do Azure localizados na mesma região não estão sujeitas à cobrança.

- Na elaboração deste documento, todas as transferências de dados de entrada não estavam sujeitas à cobrança.

- O custo das transações do ACS é insignificante na execução de operações do sistema de mensagens em filas do Barramento de Serviço. O Barramento de Serviço adquire um token do ACS por uma única instância do objeto de fábrica do sistema de mensagens. O token é então reutilizado até expirar, depois de cerca de 20 minutos. Portanto, o volume de operações do sistema de mensagens no Barramento de Serviço não é diretamente proporcional à quantidade de transações de ACS necessárias para oferecer suporte a essas operações.

- Dado o suporte à sondagem longa, usar as filas do Barramento de Serviço pode ser econômico em situações em que a entrega de baixa latência é necessária.

>[AZURE.NOTE]Todos os custos estão sujeitos a alterações. Esta tabela reflete os preços atuais no momento da elaboração deste artigo e não inclui nenhuma oferta promocional que possa estar disponível no momento. Para obter informações atualizadas sobre os preços do Azure, consulte a página de [preços do Azure](http://azure.microsoft.com/pricing/). Para obter mais informações sobre o preço do Barramento de Serviço, consulte [Preço do Barramento de Serviço]((http://azure.microsoft.com/pricing/details/service-bus/).

## Conclusão

Com uma compreensão mais aprofundada das duas tecnologias, você poderá tomar uma decisão mais informada sobre qual tecnologia de fila usar, e quando. A decisão sobre quando usar as Filas do Azure ou as filas do Barramento de Serviço depende claramente de vários fatores. Esses fatores podem depender consideravelmente das necessidades individuais de seu aplicativo e respectiva arquitetura. Se seu aplicativo já utiliza os principais recursos do Microsoft Azure, talvez você prefira optar pelas Filas do Azure, especialmente se precisar de comunicação básica e sistema de mensagens entre serviços ou filas necessárias que podem ter mais de 80 GB.

Como as filas do Barramento de Serviço fornecem vários recursos avançados, como sessões, transações, detecção de duplicidade, mensagens mortas automáticas e recursos de publicação/assinatura duráveis, elas podem ser uma opção melhor se você estiver criando um aplicativo híbrido ou se seu aplicativo de alguma forma exigir esses recursos.

## Próximas etapas

Os artigos a seguir fornecem mais orientação e informações sobre como usar as Filas do Azure ou as filas do Barramento de Serviço.

- [Como usar as filas do Barramento de Serviço](service-bus-dotnet-how-to-use-queues.md)
- [Como usar o serviço Armazenamento de Fila](../storage-dotnet-how-to-use-queues.md)
- [Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço](https://msdn.microsoft.com/library/hh528527.aspx)
- [Introdução a filas e tópicos no Barramento de Serviço do Azure](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [O Guia do Desenvolvedor do Barramento de Serviço](http://www.cloudcasts.net/devguide/)
- [”Aprofundamento em tabelas e filas do Azure”](http://www.microsoftpdc.com/2009/SVC09)
- [Arquitetura do Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Usando o serviço de enfileiramento do Azure ](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Noções básicas sobre a cobrança de armazenamento do Azure — largura de banda, transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)
 

<!---HONumber=July15_HO2-->