<properties 
   pageTitle="O que é o StorSimple? | Microsoft Azure"
	description="Descreve o gerenciamento de dados StorSimple e processo de proteção, benefícios e arquitetura e apresenta os componentes do StorSimple."
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
	ms.date="08/26/2015"
	ms.author="v-sharos@microsoft.com"/>

# Série 8000 StorSimple: uma solução de armazenamento em nuvem híbrida 

## Visão geral

O Microsoft Azure StorSimple é uma solução eficiente, econômica e gerenciável que elimina muitos dos problemas e dos custos associados à proteção de dados e ao armazenamento empresarial. Ele usa um dispositivo proprietário (o dispositivo Microsoft Azure StorSimple), integração com serviços de nuvem e um conjunto de ferramentas de gerenciamento para proporcionar uma visualização perfeita de todo o armazenamento de empresa, incluindo armazenamento em nuvem.

O StorSimple usa armazenamento em camadas para gerenciar os dados armazenados em várias mídias de armazenamento. O conjunto de trabalho atual é armazenado no local em SSDs (unidades de estado sólido), dados que são usados com menos frequência são armazenados em HDDs (unidades de disco rígido) e dados de arquivamento são enviados para a nuvem. Além disso, StorSimple usa eliminação de duplicação e compactação para reduzir a quantidade de armazenamento que consome os dados. O processo de armazenamento em camadas ocorre da seguinte maneira:

1. Um administrador do sistema configura uma conta de armazenamento em nuvem do Microsoft Azure.
2. O administrador usa o console serial e o serviço StorSimple Manager (em execução no Portal de Gerenciamento do Azure) para configurar o dispositivo e o servidor de arquivos, criando políticas de proteção de volumes e dados. O servidor de arquivos local usa iSCSI (Internet Small Computer System Interface) para acessar o dispositivo StorSimple.
3. Inicialmente, o StorSimple armazena dados na camada de SSD rápida do dispositivo.
4. À medida que a camada de SSD se aproxima da capacidade, o StorSimple elimina a duplicação e compacta os blocos de dados mais antigos, movendo-os para a camada de HDD.
5. À medida que a camada de HDD se aproxima da capacidade, o StorSimple criptografa os blocos de dados mais antigos e os envia com segurança para a conta de armazenamento do Microsoft Azure via HTTPS.
6. O Microsoft Azure cria várias réplicas dos dados em seu data center e em um data center remoto, garantindo que os dados podem ser recuperados em caso de desastre. 
7. Quando o servidor de arquivos solicita dados armazenados na nuvem, o StorSimple os recupera perfeitamente e armazena uma cópia na camada de SSD do dispositivo StorSimple.

Além do gerenciamento de armazenamento, os recursos de proteção de dados StorSimple permitem que você crie backups agendados e sob demanda e os armazene localmente ou na nuvem. Os backups são realizados na forma de instantâneos incrementais, o que significa que eles podem ser criados e restaurados rapidamente. Os instantâneos em nuvem podem ser extremamente importantes em cenários de recuperação de desastres, pois substituem os sistemas de armazenamento secundário (como backup em fita) e permitem restaurar dados para o data center ou para locais alternativos, se necessário.

>[AZURE.NOTE]A série StorSimple 8000 com atualização de software 1 ou posterior oferece suporte aos serviços de nuvem Amazon S3 com RRS, HP e OpenStack, bem como ao Microsoft Azure. (Você ainda precisará uma conta de armazenamento do Microsoft Azure para fins de gerenciamento de dispositivos.) Para obter mais informações, consulte [Configurar uma nova conta de armazenamento para o serviço](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).

## Por que usar StorSimple?

O Microsoft Azure StorSimple fornece os seguintes benefícios:

- **Integração transparente** – o Microsoft Azure StorSimple usa o protocolo iSCSI para vincular recursos de armazenamento de dados de forma invisível. Isso garante que os dados armazenados na nuvem, no data center ou em servidores remotos pareçam estar armazenados em um único local.
- **Redução dos custos de armazenamento** – o Microsoft Azure StorSimple aloca armazenamento na nuvem ou local suficiente para atender às demandas atuais e amplia o armazenamento em nuvem somente quando necessário. Ele reduz ainda mais os requisitos de armazenamento e as despesas, eliminando versões redundantes dos mesmos dados (eliminação de duplicação) e usando compactação.
- **Gerenciamento simplificado de armazenamento** – o Microsoft Azure StorSimple fornece ferramentas de administração do sistema que você pode usar para configurar e gerenciar dados armazenados localmente, em um servidor remoto e na nuvem. Além disso, você pode gerenciar o backup e restaurar as funções de um snap-in do Microsoft Management Console (MMC). O StorSimple fornece uma interface opcional separada que você pode usar para estender os serviços StorSimple de proteção de dados e gerenciamento ao conteúdo armazenado em servidores SharePoint. 
- **Melhorias na recuperação de desastres e conformidade** – o Microsoft Azure StorSimple não exige muito tempo para recuperação. Em vez disso, ele restaura os dados conforme necessário. Isso significa que as operações normais podem continuar com um mínimo de interrupção. Além disso, você pode configurar políticas para especificar agendamentos de backup e retenção de dados.
- **Mobilidade de dados** – os dados carregados nos Serviços de Nuvem do Microsoft Azure podem ser acessados de outros locais para fins de recuperação e migração. Além disso, você pode usar o StorSimple para configurar dispositivos virtuais StorSimple em máquinas virtuais (VMs) em execução no Microsoft Azure. As VMs podem usar dispositivos virtuais para acessar dados armazenados para fins de teste ou recuperação. 

O diagrama a seguir fornece uma exibição de alto nível da solução Microsoft Azure StorSimple.

![Arquitetura do StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Arquitetura do Microsoft Azure StorSimple**

## Componentes do StorSimple

A solução Microsoft Azure StorSimple inclui os seguintes componentes:

- **Dispositivo do Microsoft Azure StorSimple** – uma matriz de armazenamento híbrido local que contém SSDs (unidades de estado sólido) e HDDs (unidades de disco rígido), juntamente com controladores redundantes e recursos de failover automático. Os controladores de gerenciam o armazenamento hierárquico, colocando dados usados no momento (ou mais acessados) no armazenamento local (no dispositivo ou em servidores locais), ao mesmo tempo movendo dados menos dados para a nuvem.
- **Dispositivo virtual StorSimple** – também conhecido como Solução de Virtualização StorSimple, é uma versão de software do dispositivo StorSimple que replica a arquitetura e os recursos do dispositivo de armazenamento híbrido físico. O dispositivo virtual StorSimple é executado em um único nó em uma máquina virtual do Azure. O dispositivo virtual é adequado para uso em cenários pequenos de piloto e teste. Você não pode criar um dispositivo virtual StorSimple em um servidor local ou em um dispositivo StorSimple.
- **Windows PowerShell para StorSimple** – uma interface de linha de comando que você pode usar para gerenciar o dispositivo StorSimple. O Windows PowerShell para StorSimple tem recursos que permitem registrar seu dispositivo StorSimple, configurar a interface de rede em seu dispositivo, instalar determinados tipos de atualizações, solucionar problemas de seu dispositivo acessando a sessão de suporte e alterar o estado do dispositivo. Você pode acessar o Windows PowerShell para StorSimple conectando-se ao console serial ou usando o Windows PowerShell remotamente.
- **cmdlets Azure PowerShell StorSimple** – um conjunto de cmdlets do Windows PowerShell que permitem automatizar tarefas de nível de serviço e migração na linha de comando. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para a [referência do cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **Serviço StorSimple Manager** – uma extensão do Portal de Gerenciamento do Azure que permite gerenciar um dispositivo StorSimple ou o dispositivo de virtualização StorSimple em uma única interface da Web. Você pode usar o serviço StorSimple Manager para criar e gerenciar serviços, exibir e gerenciar dispositivos, exibir alertas, gerenciar volumes e exibir e gerenciar políticas de backup e o catálogo de backup.
- **StorSimple Snapshot Manager** – um snap-in do MMC que usa grupos de volume e o Serviço de Cópias de Sombra de Volume do Windows para gerar backups consistentes com o aplicativo. Além disso, você pode usar o Gerenciador de instantâneos do StorSimple para criar o clone e agendamentos de backup ou restaurar volumes. 
- **StorSimple Adapter for SharePoint** – uma ferramenta que estende de modo transparente o armazenamento e a proteção de dados do Microsoft Azure StorSimple para os farms do SharePoint Server, tornando o armazenamento do StorSimple visualizável e gerenciável por meio do Portal de Administração do SharePoint.

## Próximas etapas

Saiba mais sobre o [StorSimple](https://azure.microsoft.com/documentation/services/storsimple/).

Leia mais sobre os [componentes e a terminologia do StorSimple](storsimple-components.md).


 

<!---HONumber=August15_HO9-->