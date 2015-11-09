<properties 
   pageTitle="O que é o StorSimple? | Microsoft Azure" 
   description="Descreve o processo de camadas do StorSimple, o gerenciamento de dispositivos, de dispositivos virtuais, de serviços e de armazenamento, e também apresenta os principais termos usados na solução." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/14/2015"
   ms.author="v-sharos@microsoft.com"/>

# Série 8000 StorSimple: uma solução de armazenamento em nuvem híbrida 

## Visão geral

Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre os dispositivos local e o armazenamento em nuvem do Microsoft Azure. O StorSimple é uma solução de rede de área de armazenamento (SAN) eficiente, econômica e facilmente gerenciável que elimina vários problemas e despesas associados à proteção de dados e ao armazenamento empresarial. Ele usa o dispositivo proprietário StorSimple 8000 series, é integrado aos serviços de nuvem e oferece um conjunto de ferramentas de gerenciamento para proporcionar uma exibição perfeita de todo o armazenamento empresarial, incluindo o armazenamento em nuvem. (As informações de implantação do StorSimple publicadas no site do Microsoft Azure se aplicam apenas aos dispositivos StorSimple 8000 series. Se você estiver usando um dispositivo StorSimple 5000/7000 series, acesse [Ajuda do StorSimple](http://onlinehelp.storsimple.com/)).

O StorSimple usa [armazenamento em camadas](#automatic-storage-tiering) para gerenciar os dados armazenados em várias mídias de armazenamento. O conjunto de trabalho atual é armazenado no local em SSDs (unidades de estado sólido), dados que são usados com menos frequência são armazenados em HDDs (unidades de disco rígido) e dados de arquivamento são enviados para a nuvem. Além disso, o StorSimple usa a eliminação de duplicação e a compactação para reduzir a quantidade de armazenamento consumido pelos dados. Para saber mais, acesse [Eliminação de duplicação e compactação](#deduplication-and-compression). Para obter definições dos outros termos e conceitos importantes usados na documentação do StorSimple série 8000, acesse [Terminologia do StorSimple](#storsimple-terminology) no final deste artigo.

Além do gerenciamento de armazenamento, os recursos de proteção de dados StorSimple permitem que você crie backups agendados e sob demanda e os armazene localmente ou na nuvem. Os backups são realizados na forma de instantâneos incrementais, o que significa que eles podem ser criados e restaurados rapidamente. Os instantâneos em nuvem podem ser extremamente importantes em cenários de recuperação de desastres, pois substituem os sistemas de armazenamento secundário (como backup em fita) e permitem restaurar dados para o data center ou para locais alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Assista ao vídeo que apresenta uma rápida introdução ao Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## Por que usar StorSimple?

A tabela a seguir descreve algumas das principais vantagens do Microsoft Azure StorSimple.

| Recurso | Benefício |
|---------|---------|
|Integração transparente | O Microsoft Azure StorSimple usa o protocolo iSCSI para vincular recursos de armazenamento de dados de forma invisível. Isso garante que os dados armazenados na nuvem, no data center ou em servidores remotos pareçam estar armazenados em um único local.|
|Redução nos custos de armazenamento|O Microsoft Azure StorSimple aloca armazenamento na nuvem ou local suficiente para atender às demandas atuais e amplia o armazenamento em nuvem somente quando necessário. Ele reduz ainda mais os requisitos de armazenamento e as despesas, eliminando versões redundantes dos mesmos dados (eliminação de duplicação) e usando compactação.|
|Gerenciamento simplificado do armazenamento|O Microsoft Azure StorSimple fornece ferramentas de administração do sistema que você pode usar para configurar e gerenciar dados armazenados localmente, em um servidor remoto e na nuvem. Além disso, você pode gerenciar o backup e restaurar as funções de um snap-in do Microsoft Management Console (MMC). O StorSimple fornece uma interface opcional separada que você pode usar para estender os serviços StorSimple de proteção de dados e gerenciamento ao conteúdo armazenado em servidores SharePoint. |
|Aprimoramento da recuperação de desastres e conformidade|O Microsoft Azure StorSimple não exige muito tempo para recuperação. Em vez disso, ele restaura os dados conforme necessário. Isso significa que as operações normais podem continuar com um mínimo de interrupção. Além disso, você pode configurar políticas para especificar agendamentos de backup e retenção de dados.
|Mobilidade de dados|Os dados carregados nos Serviços de Nuvem do Microsoft Azure podem ser acessados de outros locais para fins de recuperação e migração. Além disso, você pode usar o StorSimple para configurar dispositivos virtuais StorSimple em máquinas virtuais (VMs) em execução no Microsoft Azure. As VMs podem usar dispositivos virtuais para acessar dados armazenados para fins de teste ou recuperação.|
|Suporte para outros provedores de serviços de nuvem |A série StorSimple 8000 com atualização de software 1 ou posterior oferece suporte aos serviços de nuvem Amazon S3 com RRS, HP e OpenStack, bem como ao Microsoft Azure. (Você ainda precisará uma conta de armazenamento do Microsoft Azure para fins de gerenciamento de dispositivos.) Para saber mais, acesse [Novidades na Atualização 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuidade dos negócios | A Atualização 1.2 também fornece um novo recurso de migração que permite aos usuários do StorSimple 5000-7000 series migrar os dados para um dispositivo StorSimple 8000 series.|
|Disponibilidade no Portal do Azure Governamental | Agora, o StorSimple está disponível no Portal do Azure Governamental. Para saber mais, consulte [Implantar seu dispositivo StorSimple local no Portal Governamental](storsimple-deployment-walkthrough-gov.md).|
|Suporte para Armazenamento de com redundância de zona | Com a atualização para a versão mais recente das APIs de armazenamento, o StorSimple 8000 series oferecerá suporte para ZRS (armazenamento com redundância de zona), além de LRS (armazenamento com redundância local) e GRS (Armazenamento com redundância geográfica). Consulte [este artigo sobre opções de redundância de Armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) para obter detalhes do ZRS.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Assista a [este vídeo](http://azure.microsoft.com/documentation/videos/guru-pangals-welcome-to-wpc--july-2015) para ver uma introdução aos recursos e benefícios do StorSimple série 8000.

## Componentes do StorSimple

A solução Microsoft Azure StorSimple inclui os seguintes componentes:

- **Dispositivo Microsoft Azure StorSimple** - uma matriz de armazenamento híbrido local que contém SSDs (unidades de estado sólido) e HDDs (unidades de disco rígido), juntamente com controladores redundantes e recursos de failover automático. Os controladores de gerenciam o armazenamento hierárquico, colocando dados usados no momento (ou mais acessados) no armazenamento local (no dispositivo ou em servidores locais), ao mesmo tempo movendo dados menos dados para a nuvem.
- **Dispositivo virtual StorSimple** – também conhecido como StorSimple Virtual Appliance, essa é uma versão de software do dispositivo StorSimple que replica a arquitetura e a maioria dos recursos do dispositivo de armazenamento híbrido físico. O dispositivo virtual StorSimple é executado em um único nó em uma máquina virtual do Azure. O dispositivo virtual é adequado para uso em cenários pequenos de piloto e teste. 
- **Serviço StorSimple Manager** – uma extensão do Portal de Gerenciamento do Azure que permite gerenciar um dispositivo StorSimple ou o dispositivo de virtualização StorSimple em uma única interface da Web. Você pode usar o serviço StorSimple Manager para criar e gerenciar serviços, exibir e gerenciar dispositivos, exibir alertas, gerenciar volumes e exibir e gerenciar políticas de backup e o catálogo de backup.
- **Windows PowerShell para StorSimple** – uma interface de linha de comando que você pode usar para gerenciar o dispositivo StorSimple. O Windows PowerShell para StorSimple tem recursos que permitem registrar seu dispositivo StorSimple, configurar a interface de rede em seu dispositivo, instalar determinados tipos de atualizações, solucionar problemas de seu dispositivo acessando a sessão de suporte e alterar o estado do dispositivo. Você pode acessar o Windows PowerShell para StorSimple conectando-se ao console serial ou usando o Windows PowerShell remotamente.
- **Cmdlets Azure PowerShell StorSimple** – um conjunto de cmdlets do Windows PowerShell que permitem automatizar tarefas de nível de serviço e migração na linha de comando. Para saber mais sobre os cmdlets do Azure PowerShell para StorSimple, acesse a [referência do cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **StorSimple Snapshot Manager** – um snap-in do MMC que usa grupos de volume e o Serviço de Cópias de Sombra de Volume do Windows para gerar backups consistentes com o aplicativo. Além disso, você pode usar o Gerenciador de instantâneos do StorSimple para criar o clone e agendamentos de backup ou restaurar volumes. 
- **StorSimple Adapter for SharePoint** – uma ferramenta que estende de modo transparente o armazenamento e a proteção de dados do Microsoft Azure StorSimple para os farms do SharePoint Server, tornando o armazenamento do StorSimple visualizável e gerenciável por meio do Portal de Administração do SharePoint.

O diagrama abaixo fornece uma exibição de alto nível da arquitetura e dos componentes do Microsoft Azure StorSimple.

![Arquitetura do StorSimple](./media/storsimple-overview/overview-big-picture.png)

As seções a seguir descrevem cada um dos componentes com bastante detalhes e explicam como a solução organiza dados, aloca armazenamento e facilita o gerenciamento de armazenamento e a proteção de dados. A última seção fornece definições de alguns termos e conceitos importantes relacionados aos componentes do StorSimple e seu gerenciamento.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Assista a [este vídeo](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) para ver uma introdução ao dispositivo StorSimple, o dispositivo virtual StorSimple e o serviço StorSimple Manager.

## Dispositivo StorSimple

O dispositivo do Microsoft Azure StorSimple é uma matriz de armazenamento híbrido local que fornece armazenamento primário e acesso a iSCSI aos dados armazenados nele. Ele gerencia a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados na solução Microsoft Azure StorSimple.

O dispositivo StorSimple inclui unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), bem como suporte para clustering e failover automático. Ele contém um processador compartilhado, armazenamento compartilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

- Conexão com um computador host
- Até seis portas de rede para conexão à rede local (LAN)
- Monitoramento de hardware
- Memória de acesso aleatório não volátil (NVRAM), que mantém as informações mesmo que a alimentação seja interrompida
- Atualização com suporte a cluster para gerenciar atualizações de software em servidores em um cluster de failover para que as atualizações tenham pouco ou nenhum efeito sobre a disponibilidade do serviço
- Serviço de cluster, que funciona como um cluster de back-end, oferecendo alta disponibilidade e minimizando os efeitos adversos que podem ocorrer se um HDD ou SSD falhar ou ficar offline

Somente um controlador está ativo em qualquer momento no tempo. Se o controlador ativo falhar, o segundo controlador ficará ativo automaticamente.

Para obter mais informações, consulte [Status e componentes de hardware do StorSimple](storsimple-monitor-hardware-status.md).

## Dispositivo virtual StorSimple

Você pode usar o StorSimple para criar um dispositivo virtual que replique a arquitetura e os recursos do dispositivo de armazenamento híbrido real. O dispositivo virtual StorSimple (também conhecido como StorSimple Virtual Appliance) é executado em um único nó em uma máquina virtual do Azure. (Um dispositivo virtual só pode ser criado em uma máquina virtual do Azure. Você não pode criá-lo em um servidor local ou em um dispositivo StorSimple.)

O dispositivo virtual tem os seguintes recursos:

- Ele se comporta como um dispositivo físico e pode oferecer uma interface iSCSI para máquinas virtuais na nuvem. 
- Você pode criar uma quantidade ilimitada de dispositivos virtuais na nuvem e ativá-los ou desativá-los como o necessário. 
- Eles podem ajudar a simular ambientes locais em cenários de recuperação de desastres e de desenvolvimento ou teste, e podem ajudar na recuperação no nível do item de backups. 

O dispositivo virtual StorSimple difere de um dispositivo StorSimple físico da seguinte maneira:

- O dispositivo virtual tem apenas uma interface, enquanto o dispositivo físico tem seis interfaces de rede. 
- Você registra o dispositivo virtual durante a configuração do dispositivo, e não como uma tarefa separada.
- Atualmente, a aplicação de atualizações no dispositivo virtual está desabilitada. Se você precisar de um dispositivo virtual com a versão mais recente, crie um novo e execute o failover de contêineres de volume para o novo dispositivo virtual.

Para obter mais informações, consulte [Dispositivo virtual do StorSimple](storsimple-virtual-device.md).

## Serviço StorSimple Manager

O Microsoft Azure StorSimple fornece uma interface do usuário baseada na web (o serviço StorSimple Manager) que permite gerenciar de modo centralizado o data center e o armazenamento em nuvem. Você pode usar o serviço StorSimple Manager para executar as seguintes tarefas:

- Definir configurações do sistema para dispositivos StorSimple.
- Configurar e gerenciar configurações de segurança para dispositivos StorSimple.
- Configurar credenciais e propriedades de nuvem.
- Configurar e gerenciar volumes em um servidor.
- Configurar grupos de volumes.
- Fazer backup e restaurar dados.
- Monitorar o desempenho.
- Revisar as configurações do sistema e identificar possíveis problemas.

Você pode usar o serviço StorSimple Manager para executar todas as tarefas de administração, exceto aquelas que exigem inatividade, como configuração inicial e instalação de atualizações do sistema.

Para saber mais, consulte [Usar o serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

## Windows PowerShell para StorSimple

O Windows PowerShell para StorSimple fornece uma interface de linha de comando que você pode usar para criar e gerenciar o serviço Microsoft Azure StorSimple e configurar e monitorar dispositivos do StorSimple. É uma interface de linha de comando com base no Windows PowerShell que inclui cmdlets dedicados para gerenciar seu dispositivo StorSimple. O Windows PowerShell para StorSimple tem recursos que permitem:

- Registrar um dispositivo.
- Configurar a interface de rede em um dispositivo.
- Instalar determinados tipos de atualizações.
- Solucionar problemas do dispositivo acessando a sessão de suporte.
- Alterar o estado do dispositivo.

Você pode acessar o Windows PowerShell para StorSimple em um console serial (em um computador host conectado diretamente ao dispositivo) ou remotamente usando o Windows PowerShell. Observe que algumas tarefas do Windows PowerShell para StorSimple, como registro de dispositivo inicial, só podem ser feitas no console serial.

Para saber mais, consulte [Usar o Windows PowerShell para StorSimple para administrar seu dispositivo](storsimple-windows-powershell-administration.md).

## Cmdlets do Azure PowerShell StorSimple

Os cmdlets do Azure PowerShell StorSimple são um conjunto de cmdlets do Windows PowerShell que permite automatizar tarefas de nível de serviço e migração na linha de comando. Para saber mais sobre os cmdlets do Azure PowerShell para StorSimple, vá para a [referência do cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).

## Gerenciador de instantâneos do StorSimple

O Gerenciador de instantâneos do StorSimple é um snap-in do MMC (Console de Gerenciamento Microsoft) que você pode usar para criar cópias de backup consistentes de um ponto no tempo de dados locais e em nuvem. O snap-in é executado em um host baseado no Windows Server. Você pode usar o Gerenciador de instantâneos do StorSimple para:

- Configurar, fazer backup e excluir volumes.
- Configurar grupos de volumes para garantir que o backup dos dados seja consistente com o aplicativo.
- Gerenciar políticas de backup para que o backup dos dados seja feito em uma programação predeterminada e armazenado em um local designado (localmente ou na nuvem).
- Restaurar volumes e arquivos individuais.

Backups são capturados como instantâneos, que registram apenas as alterações desde o último instantâneo e exigem menos espaço de armazenamento do que os backups completos. Você pode criar agendas de backup ou fazer backups imediatos, conforme necessário. Além disso, você pode usar o Gerenciador de instantâneos do StorSimple para estabelecer políticas de retenção que controlem quantos instantâneos serão salvos. Se você precisar restaurar dados de um backup, o Gerenciador de instantâneos do StorSimple permite selecionar do catálogo de instantâneos locais ou em nuvem.

Se ocorrer um desastre ou precisar restaurar dados por outro motivo, o Gerenciador de instantâneos do StorSimple restaurará de forma incremental conforme necessário. A Restauração de dados não requer que você desligue o sistema inteiro ao restaurar um arquivo, substituir equipamentos ou mover operações para outro site.

Para saber mais, consulte [O que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## Adaptador do StorSimple para SharePoint

O Microsoft Azure StorSimple inclui o adaptador do StorSimple para SharePoint, um componente opcional que estende transparentemente os recursos de proteção de dados e armazenamento do StorSimple para farms de servidores do SharePoint. O adaptador funciona com um provedor de RBS (Armazenamento de Blob Remoto) e o recurso RBS do SQL Server, permitindo que você mova os Blobs para um servidor com backup feito pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple então armazena os dados BLOB localmente ou na nuvem, com base no uso.

O adaptador do StorSimple para SharePoint é gerenciado dentro do portal de Administração Central do SharePoint. Consequentemente, o gerenciamento do SharePoint permanece centralizado e todo o armazenamento parece estar no farm do SharePoint.

Para obter mais informações, consulte [Adaptador do StorSimple para SharePoint](storsimple-adapter-for-sharepoint.md).

## Tecnologias de gerenciamento de armazenamento
 
Além do dispositivo StorSimple dedicado e de um dispositivo virtual e outros componentes, o Microsoft Azure StorSimple usa as seguintes tecnologias de software para oferecer acesso rápido aos dados e para reduzir o consumo de armazenamento:

- [Camadas de armazenamento automático](#automatic-storage-tiering) 
- [Provisionamento dinâmico](#thin-provisioning) 
- [Eliminação de duplicação e compactação](#deduplication-and-compression) 

### Camadas de armazenamento automático

O Microsoft Azure StorSimple organiza os dados em camadas lógicas com base no uso atual, na idade e na relação com outros dados. Dados mais ativos são armazenados localmente, enquanto dados menos ativos e inativos são migrados automaticamente para a nuvem. O diagrama a seguir ilustra essa abordagem de armazenamento.
 
![Camadas de armazenamento do StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para habilitar o acesso rápido, o StorSimple armazena dados muito ativos (dados mais acessados) em SSDs no dispositivo StorSimple. Ele armazena os dados que são usados ocasionalmente (dados menos acessados) em HDDs no dispositivo ou em servidores no data center. Ele move para a nuvem dados inativos, dados de backup e dados guardados para fins de arquivamento ou conformidade.

O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento conforme os padrões de uso mudam. Por exemplo, algumas informações poderão ficar menos ativas ao longo do tempo. Conforme ficam cada vez menos ativas, elas são migradas do SSD para o disco rígido e, em seguida, para a nuvem. Se esses mesmos dados ficarem ativos novamente, eles serão migrados para o dispositivo de armazenamento.

O processo de armazenamento em camadas ocorre da seguinte maneira:

1. Um administrador do sistema configura uma conta de armazenamento em nuvem do Microsoft Azure.
2. O administrador usa o console serial e o serviço StorSimple Manager (em execução no Portal de Gerenciamento do Azure) para configurar o dispositivo e o servidor de arquivos, criando políticas de proteção de volumes e dados. Os computadores locais (como servidores de arquivos) usam o iSCSI (Internet Small Computer System Interface) para acessar o dispositivo StorSimple.
3. Inicialmente, o StorSimple armazena dados na camada de SSD rápida do dispositivo.
4. À medida que a camada de SSD se aproxima da capacidade, o StorSimple elimina a duplicação e compacta os blocos de dados mais antigos, movendo-os para a camada de HDD.
5. À medida que a camada de HDD se aproxima da capacidade, o StorSimple criptografa os blocos de dados mais antigos e os envia com segurança para a conta de armazenamento do Microsoft Azure via HTTPS.
6. O Microsoft Azure cria várias réplicas dos dados em seu data center e em um data center remoto, garantindo que os dados podem ser recuperados em caso de desastre. 
7. Quando o servidor de arquivos solicita dados armazenados na nuvem, o StorSimple os recupera perfeitamente e armazena uma cópia na camada de SSD do dispositivo StorSimple.

### Provisionamento dinâmico

Provisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, o StorSimple usa o provisionamento dinâmico para alocar espaço suficiente para atender às necessidades atuais. A natureza elástica de armazenamento em nuvem facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às demandas de mudança.

### Eliminação de duplicação e compactação

O Microsoft Azure StorSimple usa eliminação de duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento.

A eliminação de duplicação reduz a quantidade geral de dados armazenados, eliminando a redundância no conjunto de dados armazenado. Conforme as informações mudam, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações desnecessárias.

## Terminologia do StorSimple 

Antes de implantar sua solução de Microsoft Azure StorSimple, recomendamos que você revise os seguintes termos e definições.

### Principais termos e definições

| Termo (sigla ou abreviação) | Descrição |
| ------------------------------ | ---------------- |
| Registro de controle de acesso (ACR) | Um registro associado a um volume no dispositivo Microsoft Azure StorSimple que determina quais hosts podem se conectar a ele. A determinação se baseia no iSCSI IQN (nome qualificado) dos hosts (contidos no ACR) que estão se conectando ao dispositivo StorSimple.|
| AES-256 | Um algoritmo de AES (padrão de criptografia avançado) de 256 bits para criptografar dados enquanto eles se movem de e para a nuvem. |
| tamanho da unidade de alocação (AUS) | A menor quantidade de espaço em disco que pode ser alocada para armazenar um arquivo nos sistemas de arquivos do Windows. Se um tamanho de arquivo não for um múltiplo par do tamanho do cluster, o espaço adicional deve ser usado para armazenar o arquivo (até o próximo múltiplo do tamanho do cluster) que resulta em espaço perdido e na fragmentação do disco rígido. <br>O AUS recomendado para volumes do Azure StorSimple é de 64 KB, porque ele funciona bem com os algoritmos de eliminação de duplicação.|
| camadas de armazenamento automatizado | Mover automaticamente os dados menos ativos de SSDs para HDDs e então para uma camada na nuvem e, em seguida, permitir o gerenciamento de todo o armazenamento de uma interface de usuário central.|
| catálogo de backup | Uma coleção de backups, geralmente relacionados pelo tipo de aplicativo usado. Essa coleção é exibida na página Catálogo de Backup da IU do serviço StorSimple Manager.|
| arquivo de catálogo de backup | Um arquivo contendo uma lista de instantâneos disponíveis atualmente armazenados no banco de dados de backup do Gerenciador de instantâneos do StorSimple. |
| política de backup | Uma seleção de volumes, tipo de backup e um calendário que permite que você crie backups em um cronograma predefinido.|
| objetos binários grandes (BLOBs) | Uma coleção de dados binários armazenados como entidade única em um sistema de gerenciamento de banco de dados. BLOBs são normalmente imagens, áudio ou outros objetos de multimídia, embora o código executável binário, às vezes, é armazenado como um BLOB.|
| Protocolo CHAP (Challenge Handshake Authentication Protocol) | Um protocolo usado para autenticar o par de uma conexão, com base em um par compartilhando uma senha ou segredo. O protocolo CHAP pode ser unidirecional ou mútuo. Com o CHAP unidirecional, o destino autentica um iniciador. O CHAP mútuo requer que o destino autentique o iniciador e que o iniciador autentique o destino. | 
| clone | Uma cópia duplicada de um volume. |
|Nuvem como camada (CaaT) | Armazenamento em nuvem integrado como camada na arquitetura de armazenamento para que todo o armazenamento pareça fazer parte da rede de armazenamento empresarial.|
| provedor de serviços de nuvem (CSP) | Um provedor de serviços de computação em nuvem.|
| instantâneo de nuvem | Uma cópia pontual dos dados de volume armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo duplicado em um sistema de armazenamento externo diferente. Instantâneos de nuvem são particularmente úteis em cenários de recuperação de desastres.|
| chave de criptografia de armazenamento em nuvem | Uma senha ou chave usada pelo seu dispositivo StorSimple para acessar os dados criptografados enviados pelo seu dispositivo para a nuvem.|
| atualização com suporte a cluster | Gerenciamento de atualizações de software em servidores em um cluster de failover para que as atualizações tenham pouco ou nenhum efeito sobre a disponibilidade do serviço|
| caminho de dados | Uma coleção de unidades funcionais que realizam operações de processamento de dados inter-relacionadas.|
| desativar | Uma ação permanente que interrompe a conexão entre o dispositivo StorSimple e o serviço de nuvem associado. Instantâneos de nuvem do dispositivo permanecem após esse processo e podem ser clonados ou usados para recuperação de desastres.|
| espelhamento de disco | Replicação de volumes de disco lógico em unidades de disco rígido separadas em tempo real para garantir a disponibilidade contínua.|
| espelhamento de disco dinâmico | Replicação de volumes de disco lógico em discos dinâmicos.|
| discos dinâmicos | Um formato de volume de disco que usa o gerenciador de discos lógicos (LDM) para armazenar e gerenciar dados em vários discos físicos. Discos dinâmicos podem ser ampliados para fornecer mais espaço livre.|
| Compartimento estendido de vários discos (EBOD) | Um compartimento secundário do dispositivo Microsoft Azure StorSimple que contém discos rígidos adicionais de armazenamento.|
| provisionamento FAT | Um provisionamento de armazenamento convencional no qual o espaço de armazenamento é alocado com base em necessidades antecipadas (e geralmente está além da necessidade atual). Consulte também *provisionamento dinâmico*.|
| unidade de disco rígido (HDD) | Uma unidade que utiliza placas giratórias para armazenar dados.|
| armazenamento em nuvem híbrida | Uma arquitetura de armazenamento que usa os recursos locais e externos, incluindo armazenamento em nuvem.|
| Internet Small Computer System Interface (iSCSI) | Um padrão de rede de armazenamento baseado no protocolo de internet (IP) para vincular equipamentos ou instalações de armazenamento de dados.|
| Iniciador iSCSI | Um componente de software que permite que um computador host que executa o Windows se conecte a uma rede de armazenamento externo baseada em iSCSI.|
| Nome qualificado de iSCSI (IQN) | Um nome exclusivo que identifica um destino ou iniciador iSCSI.|
| destino iSCSI | Um componente de software que fornece subsistemas de disco de iSCSI centralizados em redes de área de armazenamento.|
| arquivamento dinâmico | Uma abordagem de armazenamento em que dados de arquivamento estão acessíveis o tempo todo (não estão armazenados fora do local em fita, por exemplo). O Microsoft Azure StorSimple usa arquivamento dinâmico.|
| instantâneo local | Um cópia pontual de dados de volume armazenados no dispositivo Microsoft Azure StorSimple.|
| Microsoft Azure StorSimple | Uma solução poderosa que consiste em um dispositivo de armazenamento do datacenter e software que permite que organizações de TI aproveitem o armazenamento em nuvem como se fosse o armazenamento do data center. O StorSimple simplifica o gerenciamento e a proteção de dados enquanto reduz os custos. Essa solução consolida o armazenamento primário, o arquivamento, o backup e a recuperação de desastres (DR) por meio da integração perfeita com a nuvem. Combinando o gerenciamento de dados em nuvem e armazenamento SAN em uma plataforma de classe empresarial, os dispositivos StorSimple tornam possível oferecer velocidade, simplicidade e confiabilidade em relação a todas as necessidades relacionadas ao armazenamento.|
| Módulo de energia e resfriamento (PCM) | Componentes de hardware do dispositivo StorSimple que consistem em fontes de alimentação e ventilador de resfriamento, logo o nome módulo de energia e resfriamento. O compartimento primário do dispositivo possui dois PCMs de 764W, enquanto o compartimento EBOD possui dois PCMs de 580W.|
| compartimento primário | Compartimento principal do seu dispositivo StorSimple que contém os controladores de plataforma de aplicativo.|
| objetivo de tempo de recuperação (RTO) | A quantidade máxima de tempo que deve ser dispendido antes que um processo ou sistema empresarial seja totalmente restaurado após um desastre.| 
|serial anexado SCSI (SAS) | Tipo de unidade de disco rígido (HDD)|
| chave de criptografia de dados do serviço | Chave disponibilizada para todo dispositivo StorSimple novo registrado com o serviço StorSimple Manager. Os dados de configuração transferidos entre o serviço StorSimple Manager e o dispositivo são criptografados usando uma chave pública e, em seguida, podem ser descriptografados apenas no dispositivo usando uma chave privada. A chave de criptografia de dados de serviço permite que o serviço obtenha essa chave privada para descriptografar.|
| chave de registro do serviço | Chave que ajuda a registrar o dispositivo StorSimple com o serviço StorSimple Manager para que ele apareça no Portal de Gerenciamento para outras ações de gerenciamento.|
| Small Computer System Interface (SCSI) | Conjunto de padrões para conectar computadores fisicamente e transmitir dados entre eles.|
| unidade de estado sólido (SSD) | Disco que não contém peças móveis; por exemplo, uma unidade flash.|
| do Azure | Conjunto de credenciais de acesso vinculadas à sua conta de armazenamento para determinado provedor de serviço de nuvem.| 
| Adaptador do StorSimple para SharePoint| Componente do Microsoft Azure StorSimple que estende transparentemente a proteção de dados e o armazenamento do StorSimple para farms de servidores do SharePoint.|
| Serviço StorSimple Manager | Extensão do Portal de Gerenciamento do Azure que permite que você gerencie seus dispositivos locais e virtuais do Azure StorSimple.|
| Gerenciador de instantâneos do StorSimple | Snap-in do console de gerenciamento Microsoft (MMC) para gerenciar operações de backup e restauração no Microsoft Azure StorSimple.|
| fazer backup | Recurso que permite ao usuário fazer um backup interativo de um volume. É uma maneira alternativa de fazer o backup manual de um volume em vez de fazer um backup automatizado por meio de uma política definida.|
| provisionamento dinâmico | Método de otimizar a eficiência com a qual o espaço de armazenamento disponível é usado em sistemas de armazenamento. No provisionamento dinâmico, o armazenamento é alocado entre vários usuários com base no espaço mínimo exigido por cada usuário em determinado momento. Consulte também *provisionamento estático*.|
| disposição em camadas | Organização de dados em agrupamentos lógicos com base no uso atual, idade e relação com outros dados. O StorSimple organiza automaticamente os dados em camadas. |
| volume | Áreas de armazenamento lógico apresentadas na forma de unidades. Os volumes do StorSimple correspondem aos volumes montados pelo host, incluindo os descobertos através do uso de iSCSI e de um dispositivo StorSimple.|
 | contêiner de volume | Grupo de volumes e as configurações que se aplicam a eles. Todos os volumes em seu dispositivo StorSimple são agrupados em contêineres de volume. As configurações de contêiner de volume incluem contas de armazenamento, configurações de criptografia para dados enviados para a nuvem com chaves de criptografia associadas e largura de banda consumida para operações que envolvem a nuvem.|
| grupo de volumes | No Gerenciador de instantâneos do StorSimple, um grupo de volumes é uma coleção de volumes configurados para facilitar o processamento de backup.|
| Serviço de cópias de sombra de volume (VSS)| Serviço de sistema operacional do Windows Server que facilita a consistência com o aplicativo, comunicando-se com aplicativos com reconhecimento de VSS para coordenar a criação de instantâneos incrementais. O VSS garante que os aplicativos estejam temporariamente inativos quando os instantâneos forem criados.|
| Windows PowerShell para StorSimple | Interface de linha de comando baseada no Windows PowerShell usada para operar e gerenciar seu dispositivo StorSimple. Mantendo alguns dos recursos básicos do Windows PowerShell, essa interface possui cmdlets dedicados adicionais que se destinam a gerenciar um dispositivo StorSimple.|


## Próximas etapas

Saiba mais sobre a [Segurança do StorSimple](storsimple-security.md).




 

 

<!---HONumber=Nov15_HO1-->