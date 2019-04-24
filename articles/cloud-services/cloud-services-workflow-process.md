---
title: Fluxo de trabalho de arquitetura de VM do Azure do Windows | Microsoft Docs
description: Este artigo fornece visão geral dos processos de fluxo de trabalho quando você implanta um serviço.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480733"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Fluxo de trabalho de arquitetura clássica de VM do Windows Azure 
Este artigo fornece uma visão geral dos processos de fluxo de trabalho que ocorrem quando você implanta ou atualizar um recurso do Azure como uma máquina virtual. 

> [!NOTE]
>O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo aborda o uso do modelo de implantação clássica.

O diagrama a seguir apresenta a arquitetura de recursos do Azure.

![Fluxo de trabalho do Azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Noções básicas de fluxo de trabalho
   
**R**. RDFE / FFE é o caminho de comunicação do usuário para a malha. RDFE (RedDog Front-End) é a API exposta publicamente que é o front-end para o Portal de gerenciamento e a API de gerenciamento de serviços, como o Visual Studio, o MMC do Azure e assim por diante.  Todas as solicitações do usuário passam por RDFE. FFE (Fabric Front-End) é a camada move solicitações de RDFE em comandos do fabric. Todas as solicitações de RDFE percorrer o FFE alcançar controladores de malha.

**B**. O controlador de malha é responsável por manter e monitorar todos os recursos no data center. Ele se comunica com agentes de host de malha na malha do envio de informações do sistema operacional, como a versão do sistema operacional convidado, pacote de serviço, configuração de serviço e estado do serviço.

**C**. O agente de Host reside no OSsystem Host e é responsável pela configuração do sistema operacional convidado e se comunicar com o agente de convidado (WindowsAzureGuestAgent) para atualizar a função de um estado de meta pretendido e fazer a pulsação comunica-se com o agente convidado. Se o agente de Host não receber a resposta da pulsação por 10 minutos, o agente de Host reinicia o sistema operacional convidado.

**C2**. WaAppAgent é responsável por instalar, configurar e atualizar WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent é responsável pelo seguinte:

1. Configurando o SO convidado incluindo firewall, ACLs, recursos LocalStorage, pacote de serviço e configuração e certificados. Configurando o SID da conta de usuário que a função será executado.
2. Comunicando o status da função à malha.
3. Iniciando WaHostBootstrapper e monitorá-lo para certificar-se de que a função está em estado de meta.

**E**. WaHostBootstrapper é responsável por:

1. Ler a configuração de função, e a partir de todas as tarefas apropriadas e processos para configurar e executar a função.
2. Monitoramento de todos os seus processos filho.
3. Gerando o evento StatusCheck sobre o processo de host de função.

**F**. IISConfigurator é executado se a função é configurada como uma função web do IIS completo (ele não será executado para funções HWC do SDK 1.2). Ele é responsável por:

1. Iniciando os serviços do IIS padrão
2. Configurar o módulo de reescrita na configuração da web
3. Configurando o AppPool para a função configurada no modelo de serviço
4. Como configurar o log do IIS para apontar para a pasta DiagnosticStore LocalStorage
5. Configuração de permissões e ACLs
6. O site reside em % roleroot%:\sitesroot\0 e os pontos de apppool para este local para executar o IIS. 

**G**. Tarefas de inicialização são definidas pelo modelo de função e iniciadas pelo WaHostBootstrapper. Tarefas de inicialização podem ser configuradas para executar de forma assíncrona em segundo plano e o bootstrapper do host iniciará a tarefa de inicialização e, em seguida, continue com as outras tarefas de inicialização. Tarefas de inicialização também podem ser configuradas para executar no modo simples (padrão) no qual o bootstrapper do host irá aguardar a tarefa de inicialização concluir a execução e retornar um código de saída de êxito (0) antes de continuar para a próxima tarefa de inicialização.

**H**. Essas tarefas fazem parte do SDK e são definidas como plug-ins na definição da função de serviço (. csdef). Quando expandido para tarefas de inicialização, o **DiagnosticsAgent** e **RemoteAccessAgent** são os únicos que cada um deles definir duas tarefas de inicialização, um regular e que tem um **/blockStartup** parâmetro. A tarefa de inicialização normal é definida como uma tarefa de inicialização em segundo plano para que ele possa executar em segundo plano enquanto a função em si está em execução. O **/blockStartup** tarefa de inicialização é definida como uma tarefa de inicialização simples, de modo que WaHostBootstrapper espera que ele seja fechado antes de continuar. O **/blockStartup** tarefa aguarda a tarefa regular termine a inicialização e, em seguida, ele será encerrado e permitir que o bootstrapper do host continuar. Isso é feito para que o diagnóstico e acesso RDP podem ser configurados antes do início de processos de função (Isso é feito por meio da tarefa /blockStartup). Isso também permite que o diagnóstico e acesso RDP para continuar a execução depois que o bootstrapper do host concluiu as tarefas de inicialização (Isso é feito por meio da tarefa Normal).

**I**. WaWorkerHost é o processo de host padrão para funções de trabalho normal. Esse processo de host hospeda todos os da função DLLs e código de ponto de entrada, como OnStart e execução.

**J**. WaWebHost é o processo de host padrão para funções da web se eles são configurados para usar o SDK 1.2 compatível Hostable Web Core (HWC). As funções podem habilitar o modo HWC, removendo o elemento de definição de serviço (. csdef). Nesse modo, código e DLLs de todo o serviço são executados do processo WaWebHost. O IIS (w3wp) não é usado e não há nenhum AppPools configurado no Gerenciador do IIS, como o IIS está hospedado dentro de WaWebHost.exe.

**K**. Wallshost é o processo de host para o código de ponto de entrada de função para funções da web que usam o IIS completo. Esse processo carrega a DLL primeiro encontrada que usa o **RoleEntryPoint** de classe e executa o código a partir dessa classe (OnStart, execução, OnStop). Qualquer **RoleEnvironment** eventos (como StatusCheck e Changed) que são criados na classe RoleEntryPoint são acionados nesse processo.

**L**. W3wp é o processo de trabalho padrão do IIS que é usado se a função é configurada para usar o IIS completo. Isso executa o AppPool configurado de IISConfigurator. Quaisquer eventos RoleEnvironment (como StatusCheck e Changed) que são criados aqui são acionados nesse processo. Observe que os eventos RoleEnvironment serão acionado em ambos os locais (Wallshost e w3wp.exe) se você assinar eventos em ambos os processos.

## <a name="workflow-processes"></a>Processos de fluxo de trabalho

1. Um usuário faz uma solicitação, como carregar arquivos. cspkg e. cscfg, informando um recurso para interromper ou fazer uma alteração de configuração, e assim por diante. Isso pode ser feito por meio do portal do Azure ou uma ferramenta que usa a API de gerenciamento de serviço, como o recurso de publicação do Visual Studio. Esta solicitação irá para RDFE fazer todos os relacionados à assinatura trabalhar e, em seguida, comunicar-se a solicitação para FFE. O restante dessas etapas de fluxo de trabalho é implantar um novo pacote e iniciá-lo.
2. FFE localiza o pool de máquinas correto (com base na entrada do cliente, como o grupo de afinidade ou local geográfico mais entrada da malha, como a disponibilidade de máquina) e se comunica com o controlador de malha mestre nesse pool de máquina.
3. O controlador de malha localiza um host que tem núcleos de CPU disponíveis (ou gira até um novo host). O pacote de serviço e a configuração é copiado para o host e o controlador de malha se comunica com o agente de host no host do sistema operacional para implantar o pacote (Configurar quedas, portas, sistema operacional convidado e assim por diante).
4. O agente de host inicia o sistema operacional convidado e se comunica com o agente convidado (WindowsAzureGuestAgent). O host envia as pulsações para o convidado para certificar-se de que a função está trabalhando para seu estado de meta.
5. WindowsAzureGuestAgent configura o sistema operacional (firewall, ACLs, LocalStorage e assim por diante) do convidado, copia um novo arquivo de configuração XML para c:\Config. e, em seguida, inicia o processo WaHostBootstrapper.
6. Para funções da web de IIS completo, WaHostBootstrapper inicia IISConfigurator e avisa para excluir qualquer AppPools existente para a função web do IIS.
7. WaHostBootstrapper lê os **inicialização** as tarefas da E:\RoleModel.xml e começa a executar tarefas de inicialização. WaHostBootstrapper aguarda até que todas as tarefas de inicialização simples tem terminado e retornou uma mensagem de "êxito".
8. Para funções da web de IIS completo, WaHostBootstrapper informa IISConfigurator para configurar o IIS AppPool e aponta para o site de `E:\Sitesroot\<index>`, onde `<index>` é um índice baseado em 0 em um número de <Sites> elementos definidos para o serviço.
9. WaHostBootstrapper iniciará o processo de host, dependendo do tipo de função:
    1. **Função de trabalho**: WaWorkerHost.exe é iniciado. WaHostBootstrapper executa o método OnStart (). Depois que ela retorna, WaHostBootstrapper começa a executar o método Run () e, em seguida, ao mesmo tempo marcará a função como pronto e o coloca no rotação do balanceador de carga (se InputEndpoints estiver definidos). WaHostBootsrapper, em seguida, entra em um loop de verificar o status da função.
    1. **Função Web do SDK 1.2 HWC**: WaWebHost é iniciado. WaHostBootstrapper executa o método OnStart (). Depois que ela retorna, WaHostBootstrapper começa a executar o método Run () e, em seguida, marca simultaneamente a função como pronto e o coloca no rotação do balanceador de carga. WaWebHost emite uma solicitação de aquecimento (/do.rd_runtime_init GET). Todas as solicitações da web são enviadas para WaWebHost.exe. WaHostBootsrapper, em seguida, entra em um loop de verificar o status da função.
    1. **Total de função Web do IIS**: aIISHost é iniciado. WaHostBootstrapper executa o método OnStart (). Depois que ela retorna, ele começa a executar o método Run () e, em seguida, marca simultaneamente a função como pronto e o coloca no rotação do balanceador de carga. WaHostBootsrapper, em seguida, entra em um loop de verificar o status da função.
10. Solicitações de entrada da web para um IIS completo da web IIS para iniciar o processo de W3WP e atender à solicitação, da mesma forma que faria em um ambiente do IIS local de gatilhos de função.

## <a name="log-file-locations"></a>Locais de arquivo de log

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Esse log contém alterações para o serviço, incluindo inicia, interrompe e as novas configurações. Se o serviço não for alterado, você pode esperar ver grandes intervalos de tempo em que esse arquivo de log.
- C:\Logs\WaAppAgent.Log.  
Esse log contém as atualizações de status e notificações de pulsação e é atualizado a cada 2 a 3 segundos.  Esse log contém uma exibição histórica do status da instância e informará a você quando a instância não estava no estado pronto.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**Logs do IIS**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Logs de eventos do Windows**

D:\Windows\System32\Winevt\Logs
 



