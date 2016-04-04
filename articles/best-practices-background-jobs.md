<properties
   pageTitle="Diretrizes de trabalhos em segundo plano | Microsoft Azure"
   description="Diretrizes sobre as tarefas em segundo plano executadas independentemente da interface do usuário."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="masashin"/>

# Diretrizes de trabalhos em segundo plano

![](media/best-practices-background-jobs/pnp-logo.png)


## Visão geral

Muitos tipos de aplicativos requerem tarefas em segundo plano executadas independentemente da interface do usuário (IU). Os exemplos incluem trabalhos em lotes, uso intensivo de processamento de tarefas e processos de longa duração, como fluxos de trabalho. Os trabalhos em segundo plano podem ser executados sem a necessidade de interação do usuário; o aplicativo pode iniciar o trabalho e, em seguida, continuar a processar solicitações interativas de usuários. Isso pode ajudar a reduzir a carga na interface do usuário do aplicativo, que pode melhorar a disponibilidade e reduzir os tempos de resposta interativa.

Por exemplo, se for necessário um aplicativo para gerar miniaturas de imagens carregadas por usuários, ele pode fazer isso como um trabalho em segundo plano e salvar a miniatura para o armazenamento ao concluir sem precisar aguardar o processo do usuário ser concluído. Da mesma forma, um usuário fazendo um pedido pode iniciar um fluxo de trabalho em segundo plano que processa o pedido, enquanto a interface do usuário permite que o usuário continue a navegação no aplicativo Web. Quando o trabalho em segundo plano for concluído, ele poderá atualizar os dados armazenados pedidos e enviar um email para o usuário para confirmar o pedido.

Ao considerar a implementação de uma tarefa como um trabalho em segundo plano, o critério principal é uma tarefa que pode ser executada sem interação do usuário e sem a interface do usuário precisar aguardar a conclusão do trabalho. As tarefas que exigem que o usuário ou a interface do usuário aguardem enquanto elas são concluídas podem não ser apropriadas como trabalhos em segundo plano.

## Tipos de trabalhos em segundo plano

Os trabalhos em segundo plano normalmente têm uma ou mais das seguintes características:

- Os trabalhos intensivos de CPU como cálculos matemáticos, análise estrutural de modelo e muito mais.
- Os trabalhos intensivos de E/S conforme executa uma série de transações de armazenamento ou arquivos de indexação.
- Os trabalhos em lotes, como atualizações de dados todas as noites ou processamento agendado.
- Os fluxos de trabalho, como preenchimento de pedidos de execução demorada ou serviços e sistemas de provisionamento.
- O processamento de dados confidenciais em que a tarefa é transferida para um local mais seguro para processamento. Por exemplo, você pode não querer processar dados confidenciais em um aplicativo Web e, em vez disso, usar um padrão como o [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx) a fim de transferir os dados para um processo em segundo plano isolado que tenha acesso ao armazenamento protegido.

## Gatilhos

Os trabalhos em segundo plano podem ser iniciados de diversas maneiras. Na verdade, todos eles se encaixam em uma das seguintes categorias:

- [**Evento controlado por gatilhos**](#event-driven-triggers). A tarefa é iniciada em resposta a um evento, normalmente uma ação executada por um usuário ou uma etapa em um fluxo de trabalho.
- [**Programação controlada por gatilhos**](#schedule-driven-triggers). A tarefa é invocada em um agendamento com base em um temporizador. Isso pode ser um agendamento recorrente ou uma invocação unitária especificada para um momento posterior

### Evento controlado por gatilhos

O evento controlado por invocação usa um gatilho para iniciar a tarefa em segundo plano. Os exemplos de uso de gatilhos acionados por eventos incluem:

- A interface do usuário ou outro trabalho coloca uma mensagem em uma fila. A mensagem contém dados sobre uma ação que tenha ocorrido, como o usuário fazer um pedido. A tarefa em segundo plano escuta nessa fila e detecta a chegada de uma nova mensagem. Ela lê a mensagem e usa os dados contidos nela como a entrada para o trabalho em segundo plano.
- A interface do usuário ou outro trabalho salva ou atualiza um valor no armazenamento. A tarefa em segundo plano monitora o armazenamento e detecta alterações. Ela lê os dados e os utiliza como entrada para o trabalho em segundo plano.
- A interface do usuário ou outro trabalho faz uma solicitação para um ponto de extremidade, um URI HTTPS ou uma API exposta como um serviço Web. Ela passa os dados necessários para concluir a tarefa em segundo plano como parte da solicitação. O ponto de extremidade ou serviço Web invoca a tarefa em segundo plano, que usa os dados como entrada.

Os exemplos típicos de tarefas adequadas ao evento controlado por invocação incluem processamento de imagem, fluxos de trabalho, envio de informações para serviços remotos, envio de mensagens de email, provisionamento de novos usuários em aplicativos de multilocatário e muito mais.

### Programação controlada por gatilhos

A invocação de programação controlada usa um temporizador para iniciar a tarefa em segundo plano. Os exemplos de uso de gatilhos acionados por programação incluem:

- Um temporizador em execução localmente no aplicativo ou, como parte do sistema operacional do aplicativo, invoca uma tarefa em segundo plano regularmente.
- Um temporizador em execução em um aplicativo diferente ou um serviço de temporizador como o Agendador do Azure, envia uma solicitação para um serviço Web ou API regularmente. O serviço Web ou API chama a tarefa em segundo plano.
- Um processo separado ou aplicativo inicia um temporizador que faz com que a tarefa em segundo plano seja invocada após um atraso especificado ou em um momento específico.

Os exemplos típicos de tarefas adequadas para invocação controlada de programação incluem rotinas de processamento em lotes, como atualização de listas de produtos relacionados para usuários com base em seus comportamentos recentes, tarefas de rotina de processamento de dados, como atualização de índices ou gerando resultados acumulados, análise de dados para relatórios diários, limpeza de retenção de dados, verificações de consistência de dados e muito mais.

Se você usar uma tarefa controlada por agenda que deve ser executada como uma única instância, lembre-se das seguintes opções:

- Se a instância de computação que está executando o Agendador (por exemplo, uma Máquina Virtual usando tarefas agendadas do Windows) for dimensionada, você terá várias instâncias do Agendador em execução e elas poderiam iniciar várias instâncias da tarefa.
- Se as tarefas forem executadas por mais tempo que o período entre os eventos do Agendador, o Agendador pode iniciar outra instância da tarefa enquanto a anterior ainda está em execução.

## Retornando os resultados

Os trabalhos em segundo plano são executados de forma assíncrona em um processo separado, ou até mesmo em um local separado, da interface do usuário ou o processo que invocou a tarefa em segundo plano. Em um cenário ideal, as tarefas em segundo plano são operações para "disparar e esquecer", e o seu progresso de execução não tem impacto sobre a interface do usuário ou o processo de chamada. Isso significa que o processo de chamada não aguarda a conclusão das tarefas e, portanto, não é possível detectar automaticamente quando a tarefa termina. Se você precisar de uma tarefa em segundo plano para se comunicar com a tarefa de chamada para indicar o progresso ou a conclusão, você deve implementar um mecanismo para isso. Alguns exemplos incluem:

- Gravar o valor do indicador de status no armazenamento acessível para a tarefa de interface do usuário ou do chamador, que pode monitorar ou verificar esse valor quando for necessário. Outros dados que a tarefa em segundo plano deve retornar para o chamador podem ser colocados no mesmo armazenamento.
- Estabeleça uma fila de resposta que a interface do usuário ou o chamador detecte. A tarefa em segundo plano pode enviar mensagens para a fila que indica o status e a conclusão. Os dados que a tarefa em segundo plano deve retornar ao chamador podem ser colocados em mensagens. Se estiver usando o Barramento de Serviço do Azure, você pode usar as propriedades **ReplyTo** e **CorrelationId** para implementar essa funcionalidade. Para obter mais informações, consulte [Correlação no Sistema de Mensagens Agenciado do Barramento de Serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Expor uma API ou um ponto de extremidade da tarefa em segundo plano que a interface do usuário ou o chamador pode acessar para obter informações de status. Os dados que a tarefa em segundo plano deve retornar ao chamador podem ser incluídos na resposta.
- Retornar a chamada de tarefa de segundo plano à interface do usuário ou ao chamador por meio de uma API para indicar o status em pontos predefinidos ou após a conclusão. Isso pode ser feito por meio de eventos gerados localmente ou publicar e assinar o mecanismo. Os dados que a tarefa em segundo plano deve retornar para o chamador podem ser incluídos na solicitação ou carga do evento.

## Ambiente de hospedagem

Você pode hospedar as tarefas em segundo plano usando uma variedade de serviços diferentes da plataforma Azure:

- [**Aplicativos Web do Azure e Azure WebJobs**](#azure-web-apps-and-webjobs). Você pode usar WebJobs para executar trabalhos personalizados com base em uma variedade de diferentes tipos de script ou programa executável dentro do contexto de um aplicativo Web.
- [**Funções de web e de trabalho dos Serviços de Nuvem do Azure**](#azure-cloud-services-web-and-worker-roles). Você pode gravar código dentro de uma função que é executada como uma tarefa em segundo plano.
- [**Máquinas Virtuais do Azure**](#azure-virtual-machines). Se você tiver um serviço do Windows ou desejar usar o Agendador de Tarefas do Windows, é comum hospedar as suas tarefas em segundo plano em uma máquina virtual dedicada.

As seções a seguir descrevem cada uma dessas opções em mais detalhes e incluem considerações para ajudá-lo a escolher a opção apropriada.

### Aplicativos Web do Azure e Azure WebJobs

Você pode usar os Azure WebJobs para executar trabalhos personalizados como tarefas em segundo plano em um aplicativo Web do Azure. Os WebJobs são executados no contexto do seu aplicativo Web como um processo contínuo, ou em resposta a um evento de gatilho do Agendador do Azure ou a fatores externos, como alterações nos blobs de armazenamento e nas filas de mensagens. Os trabalhos podem ser iniciados e interrompidos sob demanda, e serem desligados normalmente. Se um WebJob em execução continuamente falhar, ele será reiniciado automaticamente. As ações de nova tentativa e erro são configuráveis.

Ao configurar um WebJob:

- Se desejar que o trabalho responda a um gatilho acionado por evento, ele deverá ser configurado como **Executar continuamente**. O script ou programa é armazenado na pasta chamada site/wwwroot/app\_data/jobs/continuous.
- Se você quiser que o trabalho responda a um gatilho controlado por programação, ele deve ser configurado como **Executado com agendamento**. O script ou o programa é armazenado na pasta chamada site/wwwroot/app\_data/jobs/triggered.
- Se você escolher a opção **Executar sob demanda** ao configurar um trabalho, ele executará o mesmo código que a opção **Executar em um agendamento** quando você iniciá-lo.

Os Azure WebJobs são executados na área restrita do aplicativo Web, o que significa que eles podem acessar variáveis de ambiente e compartilhar informações, como cadeias de conexão com o aplicativo Web. O trabalho tem acesso ao identificador exclusivo do computador que executa o trabalho. A cadeia de conexão chamada **AzureWebJobsStorage** fornece acesso a filas de armazenamento, blobs e tabelas do Azure para dados de aplicativo e o Barramento de Serviço para mensagens e comunicação. A cadeia de conexão chamada **AzureWebJobsDashboard** fornece acesso aos arquivos de log de ação do trabalho.

Os Azure WebJobs têm as seguintes características:

- **Segurança**: os WebJobs são protegidos pelas credenciais de implantação do aplicativo Web.
- **Tipos de arquivo com suporte**: os WebJobs podem ser definidos usando scripts de comando (.cmd), arquivos em lotes (.bat), scripts do PowerShell (.ps1), scripts bash shell (.sh), scripts PHP (.PHP), scripts Python (.py), código JavaScript (.js) e programas executáveis (.exe, .jar e muito mais).
- **Implantação**: os scripts e executáveis podem ser implantados usando o Portal do Azure, criado e implantado usando o suplemento [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) para o Visual Studio ou o [Visual Studio 2013, Atualização 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs), usando o [Azure WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md), ou copiando-os diretamente para os seguintes locais:
  - para a execução acionada: site/wwwroot/app\_data/jobs/triggered/{nome do trabalho}
  - para execução contínua: site/wwwroot/app\_data/jobs/continuous/{nome do trabalho}
- **Registro em log**: Console.Out é tratado (marcado) como INFO e Console.Error como ERROR. As informações de monitoramento e diagnóstico podem ser acessadas usando o Portal do Azure e os arquivos de log podem ser baixados diretamente do site. Eles estão salvos nos seguintes locais:
  - para execução acionada: Vfs/data/jobs/continuous/jobName
  - para execução contínua: Vfs/data/jobs/triggered/jobName
- **Configuração**: os WebJobs podem ser configurados usando o portal, a API REST e o PowerShell. Um arquivo de configuração chamado settings.job no mesmo diretório-raiz que o script de trabalho pode ser usado para fornecer informações de configuração para um trabalho. Por exemplo:
  - { "stopping\_wait\_time": 60 }
  - { "is\_singleton": true }

### Considerações

- Por padrão, os WebJobs são dimensionados com o aplicativo Web. No entanto, os trabalhos podem ser configurados para serem executados em única instância definindo a propriedade de configuração **is\_singleton** como true. Os WebJobs de instância única são úteis para tarefas que você não deseja dimensionar ou executar como várias instâncias simultâneas, como reindexação, análise de dados e tarefas semelhantes.
- Para minimizar o impacto dos trabalhos no desempenho do aplicativo Web, considere a criação de uma instância vazia do Aplicativo Web do Azure em um novo Plano de Serviço de Aplicativo para hospedar WebJobs que podem ter execução longa ou consumir muitos recursos.

### Mais informações

- Os [Recursos Recomendados dos Azure WebJobs](websites-webjobs-resources.md) listam os muitos recursos, downloads e exemplos de trabalhos úteis.

## Funções de web e de trabalho dos Serviços de Nuvem do Azure

As tarefas em segundo plano podem ser executadas dentro de uma função web ou em uma função de trabalho separada. A decisão de usar uma função de trabalho deve ser baseada na consideração de escalabilidade e nos requisitos de elasticidade, vida útil da tarefa, cadência de versão, segurança, tolerância a falhas, contenção, complexidade e a arquitetura lógica. Para obter mais informações, consulte [Padrão de consolidação de recursos de computação](http://msdn.microsoft.com/library/dn589778.aspx).

Há várias maneiras de implementar tarefas em segundo plano em uma função de Serviços de Nuvem:

- Criar uma implementação da classe **RoleEntryPoint** na função e usar os seus métodos para executar tarefas em segundo plano. As tarefas são executadas no contexto de WaIISHost.exe e podem usar o método **GetSetting** da classe **CloudConfigurationManager** para carregar definições de configuração. Para obter mais informações, consulte [Ciclo de Vida (Serviços de Nuvem)](#lifecycle-cloud-services).
- Use as tarefas de inicialização para executar tarefas em segundo plano quando o aplicativo for iniciado. Para forçar as tarefas a continuar a executar em segundo plano, configure a propriedade **taskType** para **background** (se você não fizer isso, o processo de inicialização do aplicativo será interrompido e aguardará até que a tarefa seja concluída). Para obter mais informações, consulte [Executar Tarefas de Inicialização no Azure](cloud-services-startup-tasks.md).
- Use o SDK WebJobs para implementar tarefas em segundo plano como WebJobs, que são iniciados como uma tarefa de inicialização. Para obter mais informações, consulte [Comece a usar o SDK de Trabalhos Web do Azure](websites-dotnet-webjobs-sdk-get-started.md).
- Use uma tarefa de inicialização para instalar um serviço do Windows que executa uma ou mais tarefas em segundo plano. Você deve definir a propriedade **taskType** para **background** para que o serviço seja executado em segundo plano. Para obter mais informações, consulte [Executar Tarefas de Inicialização no Azure](cloud-services-startup-tasks.md).

### Executando tarefas em segundo plano na função web

A principal vantagem de executar tarefas em segundo plano na função web é a economia nos custos de hospedagem porque não há nenhum requisito para implantar funções adicionais.

### Executando tarefas em segundo plano em uma função de trabalho

A execução de tarefas em segundo plano em uma função de trabalho tem várias vantagens:

- Ela permite que você gerencie o dimensionamento separadamente para cada tipo de função. Por exemplo, talvez sejam necessárias mais instâncias de uma função web para dar suporte à carga atual, mas menos instâncias da função de trabalho que executa tarefas em segundo plano. O dimensionamento de instâncias de computação de tarefa em segundo plano separadamente das funções da interface do usuário pode reduzir o custo de hospedagem, mantendo um desempenho aceitável.
- Ele descarrega a sobrecarga de processamento de tarefas em segundo plano da função web. A função web que fornece a interface do usuário pode permanecer responsiva e pode significar que serão necessárias menos instâncias para dar suporte a um determinado volume de solicitações de usuários.
- Ela permite que você implemente a separação de preocupações. Cada tipo de função pode implementar um conjunto específico de tarefas claramente definidas e relacionadas. Isso torna a criação e a manutenção de código mais fácil porque há menos interdependência de código e funcionalidade entre cada função.
- Isso pode ajudar a isolar os dados e processos importantes. Por exemplo, as funções web que implementam a interface do usuário não precisam ter acesso a dados que são gerenciados e controlados por uma função de trabalho. Isso pode ser útil para reforçar a segurança, especialmente ao usar um padrão, como o [Gatekeeper padrão](http://msdn.microsoft.com/library/dn589793.aspx).  

### Considerações

Considere os seguintes pontos ao escolher como e onde implantar tarefas em segundo plano ao usar funções web e de trabalho de Serviços de Nuvem:

- Hospedar tarefas em segundo plano em uma função web pode economizar os custos da execução de uma função de trabalho separada apenas para essas tarefas, mas é provável que afete o desempenho e a disponibilidade do aplicativo, se houver contenção para processamento e outros recursos. Usar uma função de trabalho separada protege a função web do impacto da longa execução ou de tarefas em segundo plano com recursos intensivos.
- Se hospedar tarefas em segundo plano usando a classe **RoleEntryPoint**, você pode mover isso facilmente para outra função. Por exemplo, se você criar a classe em uma função web e, posteriormente, decidir que precisa executar as tarefas em uma função de trabalho, é possível mover a implementação da classe **RoleEntryPoint** para a função de trabalho.
- As tarefas de inicialização são projetadas para executar um programa ou um script. Implantar um trabalho em segundo plano como um programa executável pode ser mais difícil, especialmente se ele também exigir a implantação dos assemblies dependentes. Pode ser mais fácil implantar e usar um script para definir uma tarefa em segundo plano ao usar tarefas de inicialização.
- As exceções que levam uma tarefa em segundo plano a falhar têm um impacto diferente dependendo da forma como são hospedadas:
  - Se você usar a abordagem de classe **RoleEntryPoint**, uma tarefa com falha fará com que a função reinicie para que a tarefa seja reiniciada automaticamente. Isso pode afetar a disponibilidade do aplicativo. Para evitar isso, certifique-se de incluir um tratamento de exceções robusto na classe **RoleEntryPoint** e todas as tarefas em segundo plano. Use o código para reiniciar tarefas que falham onde for apropriado e lançar a exceção para reiniciar a função somente se você não puder se recuperar normalmente da falha dentro do seu código.
  - Se usar tarefas de inicialização, você é responsável por gerenciar a execução da tarefa e verificar se ela falhar.
- Gerenciar e monitorar tarefas de inicialização é mais difícil do que usar a abordagem de classe **RoleEntryPoint**. No entanto, o SDK dos Azure WebJobs inclui um painel para facilitar o gerenciamento de WebJobs que são iniciados por meio de tarefas de inicialização.

### Mais informações

- [Padrão de consolidação de recursos de computação](http://msdn.microsoft.com/library/dn589778.aspx)
- [Introdução ao SDK de Trabalhos Web do Azure](websites-dotnet-webjobs-sdk-get-started.md)

## Máquinas Virtuais do Azure

As tarefas em segundo plano podem ser implementadas de forma que as impeça de serem implantadas nos Aplicativos Web do Azure ou nos Serviços de Nuvem, ou essas opções podem não ser convenientes. Exemplos típicos são serviços do Windows, utilitários de terceiros e programas executáveis. Isso também pode incluir programas escritos para um ambiente de execução diferente daquele que hospeda o aplicativo; por exemplo, pode ser um programa Unix ou Linux que você deseja executar por meio de um aplicativo Windows ou .NET. Você pode escolher entre uma variedade de sistemas operacionais para uma máquina virtual do Azure e executar o serviço ou o executável naquela máquina virtual.

Para ajudar com a escolha de quando usar Máquinas Virtuais, confira a [Comparação de Serviço de Aplicativo, Serviços de Nuvem e Máquinas Virtuais do Azure](choose-web-site-cloud-service-vm.md). Para obter informações sobre as opções para [Máquinas Virtuais, consulte a Máquina Virtual e os tamanhos de Serviço de Nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para obter mais informações sobre os sistemas operacionais e imagens predefinidas disponíveis para Máquinas Virtuais, consulte a [Galeria de Máquinas Virtuais do Azure](https://azure.microsoft.com/gallery/virtual-machines/).

Para iniciar a tarefa em segundo plano em uma máquina virtual separada, você tem várias opções:

- Você pode executar a tarefa sob demanda diretamente do seu aplicativo, enviando uma solicitação para um ponto de extremidade que expõe a tarefa, passando em quaisquer dados que a tarefa requer. Esse ponto de extremidade invoca a tarefa.
- Você pode configurar a tarefa para que ela seja executada em uma agenda usando um agendador ou temporizador disponível em seu sistema operacional escolhido. Por exemplo, no Windows, você pode usar o Agendador de Tarefas do Windows para executar scripts e tarefas ou, se você tiver o SQL Server instalado na máquina virtual, é possível usar o SQL Server Agent para executar scripts e tarefas.
- Você pode usar o Agendador do Azure para iniciar a tarefa acrescentando uma mensagem a uma fila que a tarefa escuta ou enviando uma solicitação para uma API que a tarefa expõe.

Consulte a seção anterior [Gatilhos](#triggers) para obter mais informações sobre como você pode iniciar tarefas em segundo plano.

### Considerações

Considere os seguintes pontos ao decidir se deseja implantar tarefas em segundo plano em uma máquina virtual do Azure:

- Hospedar as tarefas em segundo plano em uma máquina virtual do Azure separada fornece flexibilidade e permite um controle preciso sobre a iniciação, execução, agendamento e alocação de recurso. No entanto, isso aumentará o custo do tempo de execução se uma máquina virtual deve ser implantada apenas para executar tarefas em segundo plano.
- Não há um recurso para monitorar as tarefas no Portal do Azure e nenhuma funcionalidade de reinicialização automatizada para tarefas que falharam, embora você possa monitorar o status básico da máquina virtual e gerenciá-la usando os [Cmdlets de gerenciamento de serviço do Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). No entanto, não há nenhum recurso para controlar os processos e threads em nós de computação. Normalmente, usar uma máquina virtual exigirá mais esforço para implementar um mecanismo que coleta dados da instrumentação na tarefa e do sistema operacional na máquina virtual. Uma solução que pode ser apropriada é usar o [System Center Management Pack para o Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Você pode considerar a criação de testes de monitoramento que são expostos por meio de pontos de extremidade HTTP. O código para esses testes pode executar verificações de integridade, coletar informações operacionais e estatísticas, ou agrupar informações de erro e retornar isso a um aplicativo de gerenciamento. Para obter mais informações, consulte o [Padrão de monitoramento de ponto de extremidade de integridade](http://msdn.microsoft.com/library/dn589789.aspx).

### Mais informações

- [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/) no Azure
- [Perguntas frequentes sobre Máquinas Virtuais do Azure](virtual-machines/virtual-machines-linux-classic-faq.md)

## Considerações sobre o design

Há vários fatores fundamentais a serem consideradas durante a criação de tarefas em segundo plano. As seções a seguir discutem o particionamento, os conflitos e a coordenação.

## Particionamento

Se decidir incluir tarefas em segundo plano em uma instância de computação existente (como um aplicativo Web, uma função Web, uma função de trabalho existente ou uma máquina virtual), você deverá considerar como isso afetará os atributos de qualidade da instância de computação e a própria tarefa em segundo plano. Esses fatores vão ajudá-lo a decidir se deseja co-localizar as tarefas com a instância de computação existente ou separá-las em uma instância de computação separada:

- **Disponibilidade**: as tarefas em segundo plano podem não precisar ter o mesmo nível de disponibilidade que outras partes do aplicativo, em particular, a interface do usuário e outras partes diretamente envolvidas na interação do usuário. As tarefas em segundo plano podem ser mais tolerantes a falhas de conexão repetida, latência e outros fatores que afetam a disponibilidade porque as operações podem ser colocadas na fila. No entanto, deve haver capacidade suficiente para impedir o backup de solicitações que poderiam bloquear as filas e afetar o aplicativo como um todo.
- **Escalabilidade**: é provável que as tarefas em segundo plano tenham um requisito de escalabilidade diferente para a interface do usuário e as partes interativas do aplicativo. Pode ser necessário o dimensionamento da interface do usuário para atender a picos de demanda, enquanto as tarefas pendentes em segundo plano podem ser concluídas em horários com menor demanda por menos instâncias de computação.
- **Resiliência**: a falha de uma instância de computação que hospeda apenas tarefas em segundo plano pode não afetar de modo fatal o aplicativo como um todo se as solicitações para essas tarefas puderem ser enfileiradas ou adiadas até que a tarefa esteja disponível novamente. Se a instância de computação e/ou as tarefas puderem ser reiniciadas dentro de um intervalo apropriado, os usuários do aplicativo não poderão ser afetados.
- **Segurança**: as tarefas em segundo plano podem ter requisitos de segurança ou restrições diferentes da interface de usuário ou de outras partes do aplicativo. Ao usar uma instância de computação separada, você pode especificar um ambiente de segurança diferente para as tarefas. Você também pode usar os padrões como Gatekeeper para isolar as instâncias de computação em segundo plano da interface do usuário para maximizar a segurança e a separação.
- **Desempenho**: você pode escolher o tipo de instância de computação para as tarefas em segundo plano para fazer a correspondência especificamente aos requisitos de desempenho das tarefas. Isso pode significar usar uma opção de computação mais barata se as tarefas não exigirem os mesmos recursos de processamento que a interface do usuário ou uma instância maior se eles exigirem recursos e capacidade adicionais.
- **Gerenciabilidade**: as tarefas em segundo plano podem ter um ritmo diferente de desenvolvimento e implantação do código do aplicativo principal ou da interface do usuário. Implantá-los para uma instância de computação separada pode simplificar as atualizações e o controle de versão.
- **Custo**: adicionar as instâncias de computação para executar tarefas em segundo plano aumenta os custos de hospedagem. Você deve considerar cuidadosamente a compensação entre a capacidade adicional e esses custos extras.

Para obter mais informações, consulte o [Padrão de eleição de líder](http://msdn.microsoft.com/library/dn568104.aspx) e o [Padrão de consumidores concorrentes](http://msdn.microsoft.com/library/dn568101.aspx).

## Conflitos

Se você tiver várias instâncias de um trabalho em segundo plano, é possível que elas competirão pelo acesso aos recursos e serviços, como bancos de dados e armazenamento. Esse acesso simultâneo pode resultar na contenção de recursos, que pode causar conflitos na disponibilidade dos serviços e na integridade dos dados no armazenamento. A contenção de recursos pode ser resolvida usando uma abordagem de bloqueio pessimista para evitar que instâncias concorrentes de uma tarefa acessem simultaneamente um serviço ou corrompam dados.

Outra abordagem para resolver conflitos é definir tarefas em segundo plano como um singleton, para que haja sempre apenas uma instância em execução. No entanto, isso elimina os benefícios de desempenho e confiabilidade que uma configuração de várias instâncias pode oferecer, especialmente se a interface do usuário puder fornecer trabalho suficiente para manter ocupada mais de uma tarefa em segundo plano. É vital garantir que a tarefa em segundo plano possa ser reiniciada automaticamente e tenha capacidade suficiente para lidar com picos de demanda. Isso pode ser obtido pela alocação de uma instância de computação com recursos suficientes, implementando um mecanismo de enfileiramento que pode armazenar solicitações para execução posterior quando a demanda diminui, ou por uma combinação dessas técnicas.

## Coordenação

As tarefas em segundo plano podem ser complexas e requerer que várias tarefas individuais sejam executadas para produzir um resultado ou para atender a todos os requisitos. É comum nesses cenários dividir a tarefa em subtarefas ou etapas menores que podem ser executadas por vários consumidores. Os trabalhos de várias etapas podem ser mais eficientes e mais flexíveis porque as etapas individuais podem ser reutilizáveis em vários trabalhos. Também é fácil adicionar, remover ou modificar a ordem das etapas.

A coordenação de várias tarefas e etapas pode ser desafiadora, mas existem três padrões comuns que você pode usar para orientar a implementação de uma solução:

- **Decomposição de uma tarefa em várias etapas reutilizáveis**. Pode ser necessário um aplicativo para executar uma variedade de tarefas de diferentes níveis de complexidade nas informações que elas processam. Uma abordagem simples, mas inflexível, para implementar esse aplicativo poderia ser executar esse processamento como módulo monolítico. No entanto, essa abordagem provavelmente reduzirá as oportunidades para refatorar o código, otimizá-lo ou reutilizá-lo se partes do mesmo processamento forem necessárias em outros lugares no aplicativo. Para obter mais informações, consulte [Tubos e filtros padrão](http://msdn.microsoft.com/library/dn568100.aspx).
- **Execução de gerenciamento das etapas de uma tarefa**. Um aplicativo pode executar tarefas que compõem uma série de etapas, algumas das quais podem invocar serviços remotos ou acessar recursos remotos. As etapas individuais podem ser independentes umas das outras, mas elas são organizadas pela lógica de aplicativo que implementa a tarefa. Para obter mais informações, consulte o [Padrão do Supervisor do Agente do Agendador](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gerenciamento de recuperação para as etapas de uma tarefa que falhou**. Um aplicativo pode precisar desfazer o trabalho executado por uma série de etapas, que juntas definem uma operação eventualmente consistente, se uma ou mais das etapas falhar. Para obter mais informações, consulte [Padrão de transação de compensação](http://msdn.microsoft.com/library/dn589804.aspx).

## Ciclo de vida (Serviços de Nuvem)

 Se você decidir implementar trabalhos em segundo plano para aplicativos de Serviços de Nuvem que usam funções da web e de trabalho usando a classe **RoleEntryPoint**, é importante entender o ciclo de vida dessa classe para usá-la corretamente.

As funções da web e de trabalho passam por um conjunto de fases distintas, como iniciar, executar e parar. A classe **RoleEntryPoint** expõe uma série de eventos que indicam quando essas fases estão ocorrendo. Use-as para inicializar, executar e interromper as tarefas personalizadas em segundo plano. O ciclo completo é:

- O Azure carrega o assembly de função e ele o procura para uma classe que deriva de **RoleEntryPoint**.
- Se ele encontrar essa classe, ele chama **RoleEntryPoint.OnStart()**. Você substitui esse método para inicializar as tarefas em segundo plano.
- Após o método **OnStart** ser concluído, o Azure chama **Application\_Start ()** no arquivo Global do aplicativo se ele estiver presente (por exemplo, Global.asax em uma função web executando ASP.NET).
- As chamadas do Azure **RoleEntryPoint.Run()** em um novo thread de primeiro plano são executadas em paralelo com o **OnStart()**. Você substitui esse método para inicializar as tarefas em segundo plano.
- Quando termina o método Run, o Azure chama primeiro **Application\_End()** no arquivo Global do aplicativo se ele estiver presente e, em seguida, chama **RoleEntryPoint.OnStop()**. Você substitui o método **OnStop** para interromper as tarefas em segundo plano, limpar recursos, descartar objetos e fechar conexões que podem ter usado as tarefas.
- O processo de host de função de trabalho do Azure está parado. Neste ponto, a função será reciclada e reiniciada.

Para obter mais detalhes e um exemplo de como usar os métodos da classe **RoleEntryPoint**, consulte [Padrão de consolidação de recursos de computação](http://msdn.microsoft.com/library/dn589778.aspx).

## Considerações

Ao planejar como você executará tarefas em segundo plano em uma função de trabalho ou web, considere os seguintes pontos:

- A implementação do método padrão **Run** na classe **RoleEntryPoint** contém uma chamada para **Thread.Sleep(Timeout.Infinite)** que mantém a função ativa indefinidamente. Se substituir o método **Run** (que é geralmente necessário para executar tarefas em segundo plano), você não deve permitir que o seu código saia do método, a menos que você deseje reciclar a instância de função.
- Uma implementação típica do método **Run** inclui código para iniciar cada uma das tarefas em segundo plano e uma construção de loop que verifica periodicamente o estado de todas as tarefas em segundo plano. Ela pode reiniciar qualquer falha ou monitorar os tokens de cancelamento que indicam que os trabalhos foram concluídos.
- Se uma tarefa em segundo plano lançar uma exceção sem tratamento, essa tarefa deve ser reciclada, permitindo que outras tarefas de segundo plano na função continuem em execução. No entanto, se a exceção for causada por corrupção de objetos fora da tarefa, como armazenamento compartilhado, a exceção deve ser tratada pela sua classe **RoleEntryPoint**, todas as tarefas devem ser canceladas e deve ser permitido que o método **Run** seja encerrado. Em seguida, o Azure reiniciará a função.
- Use o método **OnStop** para pausar ou interromper as tarefas em segundo plano e limpar os recursos. Isso pode envolver a interrupção das tarefas de execução longa ou com várias etapas e é essencial considerar como isso pode ser feito para evitar inconsistências de dados. Se uma instância de função para por algum motivo que não seja um desligamento iniciado pelo usuário, o código em execução no método **OnStop** deve ser concluído em cinco minutos antes da finalização forçada. Certifique-se de que o seu código possa ser concluído naquele momento ou que ele possa tolerar não estar em execução até a conclusão.  
- O balanceador de carga do Azure inicia o direcionamento do tráfego para a função de instância quando o método **RoleEntryPoint** retorna true. Portanto, considere colocar todo o código de inicialização no método **OnStart** para que as instâncias de função não inicializadas com êxito não recebam qualquer tráfego.
- Você pode usar as tarefas de inicialização além dos métodos da classe **RoleEntryPoint**. Você deve usar as tarefas de inicialização para inicializar as configurações que precisa alterar no balanceador de carga do Azure, pois essas tarefas serão executadas antes de a função receber quaisquer solicitações. Para obter mais informações, consulte [Executar Tarefas de Inicialização no Azure](cloud-services-startup-tasks.md).
- Se houver um erro em uma tarefa de inicialização, isso pode forçar a função a reiniciar continuamente. Isso pode impedir que você execute uma Permuta de VIP para uma versão preparada anteriormente porque a permuta requer acesso exclusivo à função, e isso não é possível obter enquanto a função está reiniciando. Para resolver esse problema:
	-  Adicione o seguinte código ao início dos métodos **OnStart** e **Run** em sua função:

	```C#
	var freeze = CloudConfigurationManager.GetSetting("Freeze");
	if (freeze != null)
	{
		if (Boolean.Parse(freeze))
	  	{
		    Thread.Sleep(System.Threading.Timeout.Infinite);
		}
	}
	```

   - Adicione a definição da configuração **Congelar** como um valor booliano para os arquivos ServiceDefinition.csdef e ServiceConfiguration.*.cscfg para a função e defina-a como **false**. Se a função entra em um modo de reinicialização repetida, você pode alterar a configuração para **true** para congelar a execução da função e permitir que ela seja trocada por uma versão anterior.

## Considerações de resiliência

As tarefas em segundo plano devem ser resilientes para fornecer serviços confiáveis ao aplicativo. Ao planejar e criar tarefas em segundo plano, considere os seguintes pontos:

- As tarefas em segundo plano devem ser capazes de lidar normalmente com as reinicializações de função ou serviço sem corromper os dados ou apresentar inconsistência no aplicativo. Para tarefas de execução longa ou com várias etapas, considere o uso de _Pontos de verificação_ salvando o estado de trabalhos no armazenamento persistente ou como mensagens em uma fila, se isso for apropriado. Por exemplo, você pode manter informações de estado em uma mensagem em uma fila e atualizar incrementalmente essas informações de estado com o andamento da tarefa para que a tarefa possa ser processada desde o último ponto de verificação conhecido, em vez de reiniciar desde o início. Ao usar as filas do Barramento de Serviço do Azure, você pode usar sessões de mensagens para habilitar o mesmo cenário. As sessões permitem salvar e recuperar o estado de processamento do aplicativo usando os métodos [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx). Para obter mais informações sobre fluxos de trabalho e processos confiáveis de várias etapas de criação, consulte [Padrão de supervisor de agente do Agendador](http://msdn.microsoft.com/library/dn589780.aspx).
- Ao usar as funções de trabalho ou web para hospedar várias tarefas em segundo plano, projete a sua substituição do método **Run** para monitorar as tarefas com falhas ou paralisadas e reiniciá-las. Quando isso não for prático e você estiver usando uma função de trabalho, force a função de trabalho a reiniciar saindo do método **Run**.
- Ao usar filas para se comunicar com as tarefas em segundo plano, as filas podem agir como um buffer para armazenar solicitações enviadas para as tarefas, enquanto o aplicativo estiver com carga maior que o normal. Isso permite que as tarefas alcancem a interface do usuário durante os períodos menos ocupados. Isso também significa que a reciclagem de função não bloqueará a interface do usuário. Para obter mais informações, consulte o [Padrão de Nivelamento de Carga Baseado em Fila](http://msdn.microsoft.com/library/dn589783.aspx). Se algumas tarefas forem mais importantes que outras, considere implementar o [Padrão de fila de prioridade](http://msdn.microsoft.com/library/dn589794.aspx) para garantir que essas tarefas sejam executadas antes daquelas menos importantes.
- As tarefas em segundo plano que são iniciadas ou processam mensagens devem ser projetadas para lidar com inconsistências, como mensagens que chegam fora de ordem, mensagens que causam um erro repetidas vezes (conhecidas como _mensagens suspeitas_) e mensagens que são entregues mais de uma vez. Considere o seguinte:
  - As mensagens que devem ser processadas em uma ordem específica, como aquelas que alteram os dados com base em seu valor existente (por exemplo, adicionando um valor a um valor existente), podem não chegar na ordem original que foram enviadas. Como alternativa, eles podem ser tratados por diferentes instâncias de uma tarefa em segundo plano em uma ordem diferente devido a cargas diferentes em cada instância. As mensagens que devem ser processadas em uma ordem específica devem incluir um número de sequência, chave ou outro indicador que as tarefas em segundo plano podem usar para garantir que elas sejam processadas na ordem correta. Se estiver usando o Barramento de Serviço do Azure, você pode usar sessões de mensagens para garantir a ordem de entrega. No entanto, é geralmente mais eficiente, quando possível, projetar o processo para que a ordem das mensagens não seja importante.
  - Normalmente, uma tarefa em segundo plano inspecionará as mensagens na fila, o que as oculta temporariamente de outros consumidores de mensagens, e excluirá as mensagens após elas terem sido processadas com êxito. Se uma tarefa em segundo plano falhar ao processar uma mensagem, essa mensagem reaparecerá na fila após o tempo limite de inspeção ter expirado, e será processada por outra instância da tarefa ou durante o próximo ciclo de processamento desta instância. Se a mensagem causar consistentemente um erro ao consumidor, ela bloqueará a tarefa, a fila e, por fim, o próprio aplicativo quando a fila ficar cheia. Portanto, é vital detectar e remover mensagens suspeitas da fila. Se você estiver usando o Barramento de Serviço do Azure, as mensagens que causam um erro podem ser movidas automaticamente ou manualmente para uma fila de inatividade associada.
  - As filas são garantidas no _mínimo uma vez_ nos mecanismos de entrega, mas elas podem entregar a mesma mensagem mais de uma vez. Além disso, se uma tarefa em segundo plano falhar após processar uma mensagem, mas antes de excluí-la da fila, a mensagem estará disponível para processamento novamente. As tarefas em segundo plano devem ser idempotentes, o que significa que processar a mesma mensagem mais de uma vez não causa um erro ou inconsistência nos dados do aplicativo. Algumas operações são naturalmente idempotentes, como a definição de um valor armazenado para um novo valor específico. No entanto, operações como adicionar um valor a um valor armazenado existente sem verificar que o valor armazenado ainda é o mesmo quando a mensagem foi enviada originalmente causará inconsistências. As filas do Barramento de Serviço do Azure podem ser configuradas para remover automaticamente as mensagens duplicadas.
  - Alguns sistemas de mensagens, como as filas de armazenamento do Azure e as filas do Barramento de Serviço do Azure, dão suporte a uma propriedade de contagem de fila que indica o número de vezes que uma mensagem foi lida na fila. Isso pode ser útil ao lidar com mensagens suspeitas e repetidas. Para obter mais informações, consulte [Prévia de mensagens assíncronas](http://msdn.microsoft.com/library/dn589781.aspx) e [Padrões de idempotência](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## Considerações sobre dimensionamento e desempenho

As tarefas em segundo plano devem oferecer desempenho suficiente para garantir que elas não bloqueiem o aplicativo nem causem inconsistências devido à operação atrasada quando o sistema estiver sob carga. Normalmente, o desempenho é aprimorado expandindo as instâncias de computação que hospedam as tarefas em segundo plano. Para planejar e criar tarefas em segundo plano, considere os seguintes pontos ligados ao desempenho e à escalabilidade:

- O Azure oferece suporte ao dimensionamento automático (escalar horizontalmente e escalar verticalmente de volta) com base na demanda atual e na carga ou em um planejamento predefinido, para Aplicativos Web, funções de trabalho e Web dos Serviços de Nuvem e implantações de Máquinas Virtuais hospedadas. Use esse recurso para garantir que o aplicativo como um todo tenha recursos suficientes de desempenho enquanto minimiza os custos de tempo de execução.
- Onde as tarefas em segundo plano têm uma funcionalidade de desempenho diferente de outras partes de um aplicativo de Serviços de Nuvem (por exemplo, a interface do usuário ou os componentes, como a camada de acesso a dados), hospedar as tarefas em segundo plano juntas em uma função de trabalho separada permite que as a interface do usuário e as funções da tarefa de segundo plano sejam dimensionadas de forma independente para gerenciar a carga. Se várias tarefas em segundo plano tiverem recursos de desempenho significativamente diferentes umas das outras, considere dividi-las em funções de trabalho separadas e dimensionar cada tipo de função independentemente, mas observe que isso pode aumentar os custos de tempo de execução em comparação com a combinação de todas as tarefas em funções menores.
- Simplesmente dimensionar as funções pode não ser suficiente para evitar a perda de desempenho sob carga. Talvez também seja necessário dimensionar as filas de armazenamento e outros recursos para impedir que um ponto único do canal geral de processamento se torne um afunilamento. Além disso, considere outras limitações, como a taxa de transferência máxima de armazenamento e outros serviços do aplicativo e as tarefas em segundo plano relacionadas.
- As tarefas em segundo plano devem ser projetadas para dimensionamento. Por exemplo, eles devem ser capazes de detectar dinamicamente o número de filas de armazenamento em uso para escutar ou enviar mensagens à fila apropriada.
- Por padrão, os WebJobs são dimensionados com a respectiva instância de Aplicativos Web do Azure associada. No entanto, se quiser que um WebJob seja executado como uma única instância, você pode criar um arquivo Settings.job que contém os dados JSON **{ "is\_singleton": true }**. Isso força o Azure a executar apenas uma instância do WebJob, mesmo se houver várias instâncias do aplicativo Web associado, o que pode ser uma técnica útil para trabalhos agendados que devem ser executados como uma única instância.

## Padrões relacionados

- [Prévia de mensagens assíncronas](http://msdn.microsoft.com/library/dn589781.aspx)
- [Diretrizes de dimensionamento automático](http://msdn.microsoft.com/library/dn589774.aspx)
- [Padrão de transação de compensação](http://msdn.microsoft.com/library/dn589804.aspx)
- [Padrão de consumidores concorrentes](http://msdn.microsoft.com/library/dn568101.aspx)
- [Diretrizes de particionamento de computação](http://msdn.microsoft.com/library/dn589773.aspx)
- [Padrão de consolidação de recursos de computação](http://msdn.microsoft.com/library/dn589778.aspx)
- [Padrão de gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx)
- [Padrão de eleição de líder](http://msdn.microsoft.com/library/dn568104.aspx)
- [Padrão de filtros e tubos](http://msdn.microsoft.com/library/dn568100.aspx)
- [Padrão de fila de prioridade](http://msdn.microsoft.com/library/dn589794.aspx)
- [Padrão de nivelamento de carga baseado em fila](http://msdn.microsoft.com/library/dn589783.aspx)
- [Padrão de supervisor de agente do Agendador](http://msdn.microsoft.com/library/dn589780.aspx)

## Mais informações

- [Dimensionamento de aplicativos do Azure com funções de trabalho](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Executando tarefas em segundo plano](http://msdn.microsoft.com/library/ff803365.aspx)
- [Ciclo de vida de inicialização de função do Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (postagem de blog)
- [Ciclo de vida da função de Serviços de Nuvem do Azure](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vídeo)
- [Introdução ao SDK de Trabalhos Web do Azure](websites-dotnet-webjobs-sdk-get-started.md)
- [Filas do Azure e filas do Barramento de Serviço – comparações e contrastes](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Como habilitar o diagnóstico em um serviço de nuvem](cloud-services-dotnet-diagnostics.md)

<!---HONumber=AcomDC_0323_2016-->