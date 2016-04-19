<properties
 pageTitle="Hub IoT HA e DR | Microsoft Azure"
 description="Descreve os recursos que ajudam a criar soluções IoT altamente disponíveis com a recuperação de desastre."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# Alta disponibilidade e recuperação de desastres do Hub IoT

Por ser um serviço do Azure, o Hub IoT fornece alta disponibilidade (HA) com redundâncias no nível de região do Azure, sem a necessidade de qualquer trabalho adicional da solução. Além disso, o Azure oferece vários recursos que ajudam a criar soluções com recursos de recuperação de desastres ou disponibilidade entre regiões, se isso for necessário. Você deve projetar e preparar suas soluções para aproveitar a vantagem desses recursos de recuperação de desastre se quiser fornecer alta disponibilidade global entre regiões para dispositivos ou usuários. O artigo [Orientação técnica de continuidade de negócios do Azure][] descreve os recursos internos do Azure para recuperação de desastre e continuidade de negócios. O documento [Recuperação de desastres e alta disponibilidade para aplicativos do Azure][] fornece uma orientação de arquitetura sobre estratégias para que os aplicativos do Azure obtenham HA e DR.

## DR do Hub IoT do Azure
Além de HA entre regiões, o Hub IoT implementa mecanismos de failover para recuperação de desastres que não exigem intervenção do usuário. A DR do Hub IoT é iniciada automaticamente e tem um RTO (objetivo de tempo de recuperação) de 2 a 26 horas e os RPOs (objetivos de ponto de recuperação) a seguir.

| Funcionalidade | RPO |
| ------------- | --- |
| Disponibilidade de serviço para operações de registro e comunicação | Possível perda de CName |
| Dados de identidade no registro de identidade de dispositivo | Perda de dados de 0 a 5 minutos |
| Mensagens do dispositivo para a nuvem | Todas as mensagens não lidas são perdidas |
| Mensagens de monitoramento de operações | Todas as mensagens não lidas são perdidas |
| Mensagens da nuvem para o dispositivo | Perda de dados de 0 a 5 minutos |
| Fila de comentários da nuvem para o dispositivo | Todas as mensagens não lidas são perdidas |

## Failover regional com o Hub IoT

Um tratamento completo das topologias de implantação em soluções IoT está fora do escopo deste artigo, mas, para fins de alta disponibilidade e de recuperação de desastres, consideraremos o modelo de implantação de *failover regional*.

Em um modelo de failover regional, o back-end da solução é executado principalmente em um datacenter, mas um Hub IoT e back-end adicionais serão implantados em outro local de datacenter para fins de failover, no caso do Hub IoT no datacenter principal sofrer uma interrupção ou no caso de a conectividade de rede do dispositivo com o datacenter primário ser interrompida. Os dispositivos usarão um ponto de extremidade de serviço secundário sempre que o gateway primário não puder ser alcançado. Com um recurso de failover entre regiões, é possível melhorar a disponibilidade da solução ultrapassando a alta disponibilidade de uma única região.

Em um alto nível, para implementar um modelo de failover regional com o Hub IoT, você precisará do seguinte.

* **Um Hub IoT secundário e lógica de roteamento do dispositivo** - no caso de uma interrupção do serviço em sua região primária, os dispositivos deverão iniciar a conexão com a região secundária. Com base na natureza consciente do estado da maioria dos serviços envolvidos, é comum os administradores de solução acionarem o processo de failover entre regiões. A melhor maneira de comunicar o novo ponto de extremidade para os dispositivos, enquanto mantém o controle do processo, é fazer com que eles verifiquem regularmente um serviço de *concierge* para o ponto de extremidade ativo atual. O serviço de concierge pode ser um aplicativo Web simples que é replicado e acessível usando técnicas de redirecionamento de DNS (por exemplo, usando o [Gerenciador de Tráfego do Azure][]).
* **Replicação do registro de identidade** - para ser usado, o Hub IoT secundário deverá conter todas as identidades de dispositivo que possam se conectar à solução. A solução deve manter backups replicados geograficamente das identidades do dispositivo e carregá-los no Hub IoT secundário antes de mudar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do Hub IoT é muito útil neste contexto. Para saber mais, confira [Guia do desenvolvedor do Hub IoT - registro de identidade][].
* **Mesclando a lógica** - quando a região primária ficar disponível novamente, o estado e os dados criados no site secundário deverão ser migrados de volta para a região primária. Isso está relacionado principalmente às identidades de dispositivo e aos metadados do aplicativo, que deverão ser mesclados ao Hub IoT principal e com todos os outros armazenamentos específicos do aplicativo na região primária. Para simplificar esta etapa, normalmente recomendamos o uso de operações idempotente. Isso minimiza os efeitos colaterais não apenas para a distribuição eventual e consistente de eventos, mas também de duplicatas ou a entrega de eventos fora de ordem. Além disso, a lógica do aplicativo deve ser projetada para tolerar possíveis inconsistências ou situações "ligeiramente" fora do estado dos dados. Isso se deve ao tempo adicional necessário para que o sistema seja “corrigido” com base nos objetivos de ponto de recuperação (RPO).

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

- [Introdução aos Hubs IoT (Tutorial)][lnk-get-started]
- [O que é o Hub IoT do Azure?][]

[Orientação técnica de continuidade de negócios do Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Recuperação de desastres e alta disponibilidade para aplicativos do Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Failsafe: Guidance for Resilient Cloud Architectures]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Gerenciador de Tráfego do Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guia do desenvolvedor do Hub IoT - registro de identidade]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é o Hub IoT do Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0406_2016-->