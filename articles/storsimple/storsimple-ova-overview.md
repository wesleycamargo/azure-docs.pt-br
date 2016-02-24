<properties
   pageTitle="Visão geral da StorSimple Virtual Array | Microsoft Azure"
   description="Descreve a StorSimple Virtual Array, uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre um dispositivo virtual local e o armazenamento em nuvem do Microsoft Azure."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/13/2016"
   ms.author="v-sharos" />

# Introdução à StorSimple Virtual Array (visualização)

## Visão geral

Bem-vindo à Matriz Virtual Microsoft Azure StorSimple, uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre um dispositivo virtual local executando em um hipervisor e o armazenamento em nuvem do Microsoft Azure. A Matriz Virtual (também conhecida como dispositivo virtual local StorSimple) é uma solução de servidor iSCSI ou servidor de arquivos eficiente, econômica e facilmente gerenciável que elimina muitos dos problemas e dos custos associados à proteção de dados e ao armazenamento empresarial. A Matriz Virtual é especialmente adequada para cenários de filiais/escritórios remotos.

>[AZURE.IMPORTANT]
>
> A StorSimple Virtual Array está em visualização e é destinada para fins de planejamento de implantação e avaliação. Não há suporte para a instalação dessa visualização em um ambiente de produção.
>
> Se você tiver quaisquer problemas com a StorSimple Virtual Array, publique-os no [fórum do MSDN do StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

Esta visão geral concentra-se na Matriz Virtual.

- Para uma visão geral do StorSimple série 8000, vá para [StorSimple série 8000: uma solução de nuvem híbrida](storsimple-overview.md). 

- Para obter informações sobre o dispositivo StorSimple série 5000/7000, visite: [Ajuda Online do StorSimple](http://onlinehelp.storsimple.com/).

A Matriz Virtual dá suporte ao protocolo SMB (bloco de mensagens de servidor) ou iSCSI. Ela é executada em sua infraestrutura existente do hipervisor e fornece camadas para a nuvem, backup na nuvem, restauração rápida, recuperação em nível de item e recursos de recuperação de desastre.

A tabela a seguir resume os recursos importantes da Matriz Virtual.

| Recurso | Matriz Virtual |
| ------- | ------------- |
|Requisitos de instalação | Usa a infraestrutura de virtualização (Hyper-V ou VMware)|
| Disponibilidade | Nó único |
| Capacidade total (incluindo nuvem) |Até 64 TB de capacidade utilizável por dispositivo virtual |
| Capacidade local | De 390 GB a 6,4 TB de capacidade utilizável por dispositivo virtual (é necessário provisionar 500 GB a 8 TB de espaço em disco)|
| Protocolos nativos | iSCSI ou SMB |
| RTO (Objetivo de Tempo de Recuperação) | iSCSI: menos de 2 minutos, independentemente do tamanho |
| RPO (Objetivo de Ponto de Recuperação) | Backups diários e backups sob demanda |
| Disposição do armazenamento em camadas | Usa o mapeamento de calor para determinar quais dados devem ser colocados nas camadas ou removidos delas |
| Suporte | Infraestrutura de virtualização com suporte pelo fornecedor |
| Desempenho | Varia de acordo com a infraestrutura subjacente |
| Mobilidade de dados | É possível restaurar para o mesmo dispositivo ou realizar recuperação em nível de item (servidor de arquivos) |
| Camadas de armazenamento | Armazenamento local de hipervisor e nuvem |
| Tamanho de compartilhamento ou volume |Camadas: até 20 TB; localmente fixados: até 2 TB |
| Instantâneos | Com controle de falhas |
| Recuperação em nível de item | Sim, os usuários podem fazer restauração de compartilhamentos |

## Por que usar StorSimple?

O StorSimple conecta usuários e servidores no armazenamento do Azure, em minutos, sem modificações em aplicativos.

A tabela a seguir descreve algumas das principais vantagens que a solução de Matriz Virtual oferece.

| Recurso | Benefício |
|---------|---------|
| Integração transparente | A Matriz Virtual dá suporte ao protocolo SMB ou iSCSI. Isso garante que os dados armazenados na nuvem, no data center ou em servidores remotos pareçam estar armazenados em um único local.|
| Redução nos custos de armazenamento | Com o StorSimple, você provisionar armazenamento local suficiente para atender às demandas atuais. Conforme as necessidades de armazenamento crescem, o StorSimple distribui dados em camadas para um armazenamento em nuvem com bom custo/benefício. Na nuvem, ele usa eliminação de duplicação e compactação de dados para reduzir ainda mais as despesas e os requisitos de armazenamento.|
| Gerenciamento simplificado do armazenamento | O StorSimple fornece gerenciamento centralizado na nuvem. Você pode usar as ferramentas baseadas na Web para configurar e gerenciar dados armazenados na Matriz Virtual, em um servidor remoto e na nuvem.| 
| Aprimoramento da recuperação de desastres e conformidade | O StorSimple não exige muito tempo para recuperação. Em vez disso, ele restaura os metadados imediatamente e os dados armazenados conforme necessário. Isso significa que as operações normais podem continuar com um mínimo de interrupção.|
| Mobilidade de dados | Os dados carregados na nuvem podem ser acessados de outros locais para fins de recuperação e migração. Observe que você pode restaurar dados somente para a Matriz Virtual original. No entanto, você usa recursos de recuperação de desastre para restaurar toda a Matriz Virtual para outra Matriz Virtual.|

## Fluxos de trabalho

A StorSimple Virtual Array é particularmente adequada para os fluxos de trabalho a seguir:

- [Gerenciamento de armazenamento baseado em nuvem](#cloud-based-storage-management)
- [Backup independente de local](#location-independent-backup)
- [Recuperação de desastre e proteção de dados](#data-protection-and-disaster-recovery)

### Gerenciamento de armazenamento baseado em nuvem

Você pode usar o serviço StorSimple Manager em execução no portal clássico do Azure para gerenciar dados armazenados em vários dispositivos e em vários locais. Isso é particularmente útil em cenários de filiais distribuídas. Observe que você deve criar instâncias separadas do serviço StorSimple Manager para gerenciar as Matrizes Virtuais e dispositivos StorSimple físicos.

![gerenciamento de armazenamento baseado em nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### Backup independente de local

Com a Matriz Virtual, instantâneos de nuvem fornecem uma cópia pontual de um volume ou compartilhamento, independente de local. Instantâneos de nuvem são habilitados por padrão e não podem ser desabilitados. O backup de todos os volumes e compartilhamentos é realizado ao mesmo tempo por meio de uma única política de backup diária, e você pode fazer backups de ad hoc adicionais sempre que necessário.

### Recuperação de desastre e proteção de dados

A Matriz Virtual dá suporte aos seguintes cenários de proteção de dados e recuperação de desastre:

- **Restauração compartilhamento ou volume** – use a restauração como novo fluxo de trabalho para recuperar um volume ou compartilhamento. Use essa abordagem para recuperar o compartilhamento ou volume inteiro.
- **Recuperação em nível de item** – compartilhamentos permitem acesso simplificado aos backups recentes. Você pode recuperar facilmente um arquivo individual de uma pasta .backup especial disponível na nuvem. Essa funcionalidade de restauração é controlada pelo usuário e nenhuma intervenção administrativa é necessária.
- **Recuperação de desastres** – use a funcionalidade de failover para recuperar todos os volumes ou compartilhamentos para uma nova Matriz Virtual. Crie a nova Matriz Virtual e registre-a com o serviço StorSimple Manager, então realize o failover da Matriz Virtual original. A nova Matriz Virtual, em seguida, assumirá os recursos provisionados. 

## Componentes da Matriz Virtual

A Matriz Virtual inclui os seguintes componentes:

- [Matriz Virtual](#virtual-array) – um dispositivo de armazenamento de nuvem híbrida com base em uma máquina virtual provisionada em seu ambiente virtualizado ou hipervisor.  
- [Serviço StorSimple Manager](#storsimple-manager-service) – uma extensão do portal clássico do Azure que permite gerenciar um ou mais dispositivos StorSimple de uma única interface da Web, que pode ser acessada por você de diferentes localizações geográficas. Você pode usar o serviço StorSimple Manager para criar e gerenciar serviços, exibir e gerenciar dispositivos e alertas, gerenciar volumes, compartilhamentos e instantâneos existentes.
- [Interface do usuário da Web local](#local-web-user-interface) – uma interface do usuário baseada na Web que é usada para configurar o dispositivo para que ele se conecte à rede local e, em seguida, registre o dispositivo com o serviço StorSimple Manager. 
- [Interface de linha de comando](#command-line-interface) – uma interface do Windows PowerShell que você pode usar para iniciar uma sessão de suporte na Matriz Virtual. As seções a seguir descrevem cada um dos componentes com bastante detalhes e explicam como a solução organiza dados, aloca armazenamento e facilita o gerenciamento de armazenamento e a proteção de dados.

### Matriz Virtual

A Matriz Virtual é uma solução de armazenamento de nó único que oferece armazenamento primário, gerencia a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados no dispositivo.

A Matriz Virtual está disponível em um modelo que está disponível para download. A matriz de armazenamento tem uma capacidade máxima de 6,4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB incluindo o armazenamento em nuvem.

A Matriz Virtual tem os seguintes recursos:

- Tem bom custo/benefício. Usa sua infraestrutura de virtualização existente e pode ser implantada em seu hipervisor Hyper-V ou VMware existente.
- Reside no datacenter e pode ser configurada como um servidor iSCSI ou um servidor de arquivos. 
- É integrada à nuvem.
- Backups são armazenados na nuvem, que pode facilitar a recuperação de desastres e simplificar a ILR (Recuperação em Nível de Item). 
- Você pode aplicar atualizações à Matriz Virtual, assim como você as aplicaria a um dispositivo físico.

>[AZURE.NOTE]Uma matriz Virtual não pode ser expandida. Portanto, é importante provisionar o armazenamento adequado ao criar o dispositivo virtual.

### Serviço StorSimple Manager

O Microsoft Azure StorSimple fornece uma interface do usuário baseada na web (o serviço StorSimple Manager) que permite gerenciar de modo centralizado o data center e o armazenamento em nuvem. Você pode usar o serviço StorSimple Manager para executar as seguintes tarefas:

- Gerenciar, de um único serviço, várias Matrizes Virtuais StorSimple. 
- Configurar e gerenciar configurações de segurança para dispositivos StorSimple. (Criptografia na nuvem é dependente de APIs do Microsoft Azure.)
- Configurar propriedades e credenciais da conta de armazenamento.
- Configurar e gerenciar volumes ou compartilhamentos.
- Fazer backup e restaurar dados em volumes ou compartilhamentos.
- Monitorar o desempenho.
- Revisar as configurações do sistema e identificar possíveis problemas.

Você pode usar o serviço StorSimple Manager para realizar a administração diária de sua Matriz Virtual.

Para saber mais, veja [Usar o serviço do StorSimple Manager para administrar seu dispositivo do StorSimple](storsimple-manager-service-administration.md).

### Interface do usuário da Web local

A Matriz Virtual inclui uma interface do usuário baseada na Web que é usada para configuração e registro do dispositivo a serem realizados uma única vez, com o serviço StorSimple Manager. Você pode usá-lo para desligar e reiniciar a Matriz Virtual, executar testes de diagnóstico, a atualização de software, alterar a senha de administrador do dispositivo, exibir logs do sistema e contatar o Suporte da Microsoft para uma solicitação de serviço.

Para obter informações sobre como usar a interface do usuário baseada na Web, vá para [Usar a interface do usuário baseada na Web para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### Interface de linha de comando

A interface incluída no Windows PowerShell permite iniciar uma sessão de suporte com o Suporte da Microsoft para que eles possam ajudá-lo a solucionar problemas e resolver problemas que podem ser encontrados em seu dispositivo virtual.

## Tecnologias de gerenciamento de armazenamento

Além da Matriz Virtual e outros componentes, a solução StorSimple usa as tecnologias de software a seguir para fornecer acesso rápido aos dados importantes, reduzir o consumo de armazenamento e proteger os dados armazenados em sua Matriz Virtual:

- [Camadas de armazenamento automático](#automatic-storage-tiering) 
- [Volumes e compartilhamentos fixados localmente](#locally-pinned-shares-and-volumes)
- [Eliminação de duplicação e compactação para dados em camadas ou em backup na nuvem](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Backups agendados e sob demanda](#scheduled-and-on-demand-backups)

### Camadas de armazenamento automático

A Matriz Virtual usa um novo mecanismo de distribuição em camadas para gerenciar dados armazenados na Matriz Virtual e na nuvem. Existem apenas duas camadas: a Matriz Virtual local e o armazenamento em nuvem do Azure. A StorSimple Virtual Array organiza os dados nas camadas com base em um mapa de calor, que controla o uso atual, idade e relações com outros dados. Dados mais ativos (mais quentes) são armazenados localmente, enquanto dados menos ativos e inativos são migrados automaticamente para a nuvem. (Todos os backups são armazenados na nuvem.) O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento conforme os padrões de uso mudam. Por exemplo, algumas informações poderão ficar menos ativas ao longo do tempo. Conforme elas se tornam cada vez menos ativas, são removidas da camada e enviadas para a nuvem. Se esses mesmos dados ficarem ativos novamente, passarão por uma nova transferência de camada indo para a Matriz de Armazenamento.

Dados para um volume ou compartilhamento hierárquico específico têm seu próprio espaço de nível local assegurado (aproximadamente 10% do espaço total provisionado para esse compartilhamento ou volume). Enquanto isso reduz o armazenamento disponível no dispositivo virtual para esse compartilhamento ou volume, garante que camadas para um compartilhamento ou volume não serão afetadas pelas necessidades de distribuição em camadas de outros volumes ou compartilhamentos. Portanto, uma carga de trabalho muito ocupada em um compartilhamento ou volume não pode forçar todas as outras cargas de trabalho para a nuvem.

![distribuição automática do armazenamento em camadas](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE]Você pode especificar um volume fixado localmente; neste caso, os dados permanecem na Matriz Virtual e nunca são distribuídos em camadas para a nuvem. Para obter mais informações, vá para [Volumes e compartilhamentos fixados localmente](#locally-pinned-shares-and-volumes).

### Volumes e compartilhamentos fixados localmente

Você pode criar os compartilhamentos e volumes apropriados como fixados localmente. Essa funcionalidade garante que os dados necessários para aplicativos críticos permaneçam na Matriz Virtual e não sejam distribuídos em camadas na nuvem. Compartilhamentos e volumes fixados localmente têm as seguintes características:

- Não estão sujeitos a problemas de conectividade ou latências na nuvem.
- Eles ainda se beneficiam dos recursos de recuperação de desastre e backup em nuvem StorSimple.

Você pode restaurar um compartilhamento ou volume fixado localmente como em camadas, ou então um compartilhamento ou volume em camadas como fixado localmente.

Para obter mais informações sobre volumes fixados localmente, vá até [Usar o serviço StorSimple Manager para gerenciar volumes](storsimple-manage-volumes-u2.md).

### Eliminação de duplicação e compactação para dados em camadas ou em backup na nuvem

O Microsoft Azure StorSimple usa eliminação de duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento na nuvem. A eliminação de duplicação reduz a quantidade geral de dados armazenados, eliminando a redundância no conjunto de dados armazenado. Conforme as informações mudam, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações duplicadas.

>[AZURE.NOTE]Os dados armazenados na Matriz Virtual não passaram por eliminação de duplicação ou compactação. Toda a eliminação de duplicação e compactação ocorrem imediatamente antes do envio dos dados para a nuvem.

### Backups agendados e sob demanda

Os recursos de proteção de dados StorSimple permitem que você crie backups sob demanda. Além disso, um agendamento padrão garante que o backup dos dados seja realizado diariamente. Backups são feitos na forma de instantâneos incrementais, que são armazenados na nuvem. Instantâneos, que registram apenas as alterações desde o último backup, podem ser criados e restaurados rapidamente. Esses instantâneos podem ser extremamente importantes em cenários de recuperação de desastres, pois substituem os sistemas de armazenamento secundário (como backup em fita) e permitem restaurar dados para o data center ou para locais alternativos, se necessário.

## Próximas etapas

Saiba como [preparar o portal de Matriz Virtual](storsimple-ova-deploy1-portal-prep.md).

<!---HONumber=AcomDC_0121_2016-->