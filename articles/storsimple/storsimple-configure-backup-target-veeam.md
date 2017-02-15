---
title: Configurar o Microsoft Azure StorSimple com Veeam | Microsoft Docs
description: "Descreve a configuração de destino de Backup do StorSimple com o Veeam."
services: storsimple
documentationcenter: 
author: hkanna
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: dfecc291c441b862499d7eaaab421c7b72f929b2
ms.openlocfilehash: 2f71afc6c60cc22ef73a902ee67466313221def2

---

# <a name="configure-storsimple-with-veeam"></a>Configurar o StorSimple com Veeam

## <a name="overview"></a>Visão geral

O Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida que resolve as complexidades do crescimento exponencial de dados. Essa solução usa uma conta de armazenamento do Azure como uma extensão da solução local e dispõe os dados em camadas automaticamente no armazenamento local e no armazenamento em nuvem.

Este artigo discute a integração do StorSimple com o Veeam e as práticas recomendadas para a integração de ambas as soluções. Também apresentamos recomendações sobre como configurar o Veeam para se integrar melhor ao StorSimple. Consultamos as melhores práticas, os arquitetos de backup e os administradores da Veeam sobre como configurar melhor o Veeam para atender às suas necessidades de backup e aos SLAs.

Este artigo ilustra os principais conceitos e as etapas de configuração, mas não é um guia passo a passo de configuração ou instalação. O artigo pressupõe que a infraestrutura e os componentes básicos estão funcionando e prontos para dar suporte aos conceitos que descrevemos.

## <a name="why-storsimple-as-a-backup-target"></a>Por que o StorSimple como um destino de backup?

O StorSimple é um ótimo destino de backup pelos seguintes motivos:

-   Ele oferece armazenamento local padrão para uso por aplicativos de backup sem alterações, o que o torna um destino de backup rápido. O StorSimple também está disponível para a restauração rápida de backups recentes.

-   Sua disposição em camadas na nuvem está totalmente integrada a uma conta de armazenamento em nuvem para usar o armazenamento econômico do Microsoft Azure.

-   Ele oferece armazenamento externo para recuperação de desastre automaticamente.


## <a name="target-audience"></a>Público-alvo

O público deste artigo inclui administradores de backup, administradores de armazenamento e arquitetos de armazenamento que conheçam os conceitos de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e Veeam.

## <a name="supported-versions"></a>Versões com suporte

-   Veeam 9 e versões posteriores.

-   [StorSimple Atualização 3 e posteriores](/storsimple-overview#storsimple-workload-summary).



## <a name="key-concepts"></a>Principais conceitos

Assim como acontece com qualquer solução de armazenamento, o sucesso depende de se fazer uma avaliação cuidadosa do desempenho do armazenamento da solução, dos SLAs, da taxa de alteração e das necessidades de aumento da capacidade. A ideia principal é que, com a introdução de uma camada de nuvem, seus horários de acesso e produtividade na nuvem terão um papel fundamental na capacidade do StorSimple de fazer seu trabalho.

O StorSimple foi projetado para oferecer armazenamento a aplicativos que operam em um conjunto de dados de trabalho bem definido (dados mais acessados). Nesse modelo, o conjunto de dados de trabalho é armazenado nas camadas locais e o conjunto de dados que não é de trabalho/menos acessado/arquivado restante é colocado em camadas na nuvem. Esse modelo é representado na figura a seguir. A linha verde quase reta representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todas as camadas. O espaço entre a linha verde reta e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

**Disposição em camadas do StorSimple**
![Diagrama da disposição em camadas do Storsimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Com essa arquitetura em mente, você descobrirá que o StorSimple é ideal para operar como um destino de backup. O StorSimple permite que você:

-   Realize restaurações mais frequentes do conjunto de dados de trabalho local.

-   Utilize a nuvem em uma recuperação de desastre externa e para dados mais antigos, para os quais as restaurações são menos frequentes.

## <a name="storsimple-benefits"></a>Benefícios do StorSimple

O StorSimple oferece uma solução local que está totalmente integrada ao Microsoft Azure, aproveitando as vantagens do acesso contínuo ao armazenamento local e em nuvem.

O StorSimple usa uma disposição em camadas automática entre o dispositivo local, que contém armazenamentos SSD (dispositivos de estado sólido), SAS (SCSI) anexado em série e Armazenamento do Azure. A disposição em camadas automática mantém os dados acessados com frequência no local, nas camadas de SSD e SAS e move os dados raramente acessados para o Armazenamento do Azure.

O StorSimple oferece os seguintes benefícios:

-   Algoritmos de eliminação de duplicação e compactação exclusivos que usam a nuvem para alcançar níveis de eliminação de duplicação sem precedentes

-   Alta disponibilidade

-   Replicação geográfica usando a replicação geográfica do Azure

-   Integração do Azure

-   Criptografia de dados na nuvem

-   Aprimoramento da recuperação de desastres e conformidade

Embora o StorSimple apresente dois cenários de implantação principal (destinos de backup primário e secundário), lembre-se de que é ele é basicamente um dispositivo de armazenamento em bloco simples. O StorSimple faz toda a compactação e eliminação de duplicação, além de enviar e recuperar dados de nuvem diretamente para o aplicativo e para o sistema de arquivos.

Para saber mais sobre o StorSimple, confira [StorSimple série 8000: solução de armazenamento de nuvem híbrida](storsimple-overview.md) e examine as [especificações técnicas da série StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Só existe suporte para o dispositivo StorSimple como destino de backup no StorSimple 8000 Atualização 3 ou posterior.

## <a name="architecture-overview"></a>Visão geral da arquitetura

As tabelas a seguir contêm as diretrizes iniciais do modelo à arquitetura do dispositivo.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>Capacidades de armazenamento local e em nuvem do StorSimple


| Capacidade de armazenamento       | 8100       | 8600       |
|------------------------|---------------|-----------------|
| Capacidade de armazenamento local | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacidade de armazenamento em nuvem | &gt;200 TiB\* | &gt;500 TiB\* |

\*O tamanho de armazenamento não assume nenhuma eliminação de duplicação ou compactação.

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>Capacidade do StorSimple para os backups primários e secundários


| Cenário de backup  | Capacidade de armazenamento local                                         | Capacidade de armazenamento em nuvem                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| Backup principal   | Backups recentes armazenados no armazenamento local para rápida recuperação (RPO) | O histórico de backup (RPO) cabe na capacidade da nuvem |
| Backup secundário | Uma cópia secundária dos dados de backup pode ser armazenada na capacidade da nuvem  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de backup principal

Nesse cenário, são apresentados os volumes do StorSimple para o aplicativo de backup como o único repositório para backups. A figura a seguir ilustra a arquitetura da solução em que todos os backups usam volumes em camadas do StorSimple para backups e restaurações.

![StorSimple como um diagrama lógico de destino de backup principal](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino principal

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.

2.  O servidor de backup grava os dados nos volumes hierárquicos do StorSimple.

3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.

4.  O script de instantâneo dispara o gerenciamento de instantâneo de nuvem do StorSimple.

5.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino principal

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.

2.  O agente de backup recebe os dados do servidor de backup.

3.  O servidor de backup conclui o trabalho de restauração.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

Nesse cenário, os volumes do StorSimple são usados principalmente para retenção de longo prazo ou arquivamento.

A figura a seguir ilustra a arquitetura em que os backups e restaurações iniciais são destinados a um volume de alto desempenho. Esses backups são copiados e arquivados em um volume em camadas do StorSimple em determinada agenda.

É importante dimensionar o volume de alto desempenho com espaço e desempenho suficientes para lidar com os requisitos de capacidade e desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino secundário

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.

2.  O servidor de backup grava dados em armazenamento de alto desempenho.

3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.

4.  O servidor de backup copia os backups no StorSimple com base em uma política de retenção.

5.  O script de instantâneo dispara o gerenciamento de instantâneo de nuvem do StorSimple.

6.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino secundário

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.

2.  O agente de backup recebe os dados do servidor de backup.

3.  O servidor de backup conclui o trabalho de restauração.

## <a name="deploy-the-solution"></a>Implantar a solução

A implantação dessa solução consiste em três etapas: preparar a infraestrutura de rede, implantar seu dispositivo StorSimple como um destino de backup e implantar o Veeam. Cada uma das etapas acima será discutida nas seções a seguir.

### <a name="configure-the-network"></a>Configurar a rede

O StorSimple, como uma solução integrada com a nuvem do Azure, requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada em operações como instantâneos de nuvem, gerenciamento, transferência de metadados e disposição de dados menos acessados em camadas no armazenamento em nuvem do Azure.

Para que a solução tenha o desempenho ideal, recomendamos seguir as práticas recomendadas de rede abaixo:

-   O link que conecta as camadas do StorSimple ao Azure deve atender aos seus requisitos de largura de banda pela aplicação adequada da QoS (qualidade de serviço) às opções de infraestrutura para corresponder aos SLAs de RPO/RTO.

-   As latências máximas de acesso ao Armazenamento de Blobs do Azure devem estar no intervalo de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para ser orientado passo a passo na implantação do StorSimple, vá para [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Implantar Veeam

Para consultar as práticas recomendadas de instalação Veeam, vá para [Práticas recomendadas para Veeam9](https://bp.veeam.expert/) e guia do usuário em [Centro de Ajuda Veeam (Documentação Técnica)](https://www.veeam.com/documentation-guides-datasheets.html)

## <a name="configure-the-solution"></a>Configurar a solução

Nesta seção, demonstraremos alguns exemplos de configuração. Os exemplos/recomendações a seguir ilustram a implementação mais básica e fundamental. Essa implementação pode não se aplicar diretamente aos seus requisitos específicos de backup.

### <a name="configure-storsimple"></a>Configurar o StorSimple

| Tarefas de implantação do StorSimple                                                                                                                 | Comentários adicionais                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Implantação do seu dispositivo StorSimple local.                                                                                    | Versão com suporte: atualização 3 e posterior.                                                                                                                                                                                                                                                                 |
| Habilitar o modo de destino de backup.                                                                                                                | Use os comandos a seguir para habilitar/desabilitar e obter o status. Para obter mais informações, consulte [conectar -se remotamente a um StorSimple](storsimple-remote-connect.md).</br> Habilitar o modo de backup:`Set-HCSBackupApplianceMode -enable`</br>  Desabilitar o modo de backup:`Set-HCSBackupApplianceMode -disable`</br> Estado atual das configurações de modo de backup`Get-HCSBackupApplianceMode` |
| Crie um contêiner de volume comum para o seu volume que armazena os dados de backup. Todos os dados de um contêiner de volume passam por eliminação de duplicação. | Contêineres de volume do StorSimple definem domínios de eliminação de duplicação.                                                                                                                                                                                                                                             |
| Criando volumes do StorSimple.                                                                                                                 | Crie volumes com tamanhos o mais próximo possível do uso antecipado, já que o tamanho do volume afeta o tempo de duração do instantâneo de nuvem. Para saber mais sobre como dimensionar um volume, confira [Políticas de retenção](#retention-policies).</br> </br> Use os volumes em camadas do StorSimple e selecione **Use este volume para dados de arquivamento acessados com menos frequência**. </br> Volumes afixados localmente apenas não têm suporte.        |
| Crie uma política de backup do StorSimple exclusiva para todos os volumes de destino de backup.                                                               | Uma política de backup do StorSimple define o grupo de consistência do volume.                                                                                                                                                                                                                                       |
| Desabilitar a agenda como instantâneos.                                                                                                    | Os instantâneos são disparados como uma operação de pós-processamento.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |


### <a name="configure-host-backup-server-storage"></a>Configurar o armazenamento do servidor de backup do host

Verifique se o armazenamento do servidor de backup do host está configurado de acordo com as diretrizes a seguir:  

- Não use volumes estendidos (criados pelo Gerenciador de Disco do Windows), pois esses volumes não tês suporte.
- Formate os volumes usando o NTFS com tamanho de unidade de alocação de 64 KB.
- Mapeie os volumes do StorSimple diretamente ao servidor Veeam. 
    - Use o iSCSI para servidores físicos.


## <a name="best-practices-for-storsimple-and-veeam"></a>Práticas recomendadas para StorSimple e Veeam

Configure sua solução de acordo com as diretrizes a seguir.

### <a name="operating-system"></a>Sistema operacional

-   Desabilite a criptografia do Windows Server e a eliminação de duplicação do sistema de arquivos NTFS.

-   Desabilite a desfragmentação do Windows Server em volumes do StorSimple.

-   Desabilite a indexação do Windows Server em volumes do StorSimple.

-   Execute uma verificação antivírus no host de origem (não em relação os volumes do StorSimple).

-   Desabilitar o padrão [manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no gerenciador de tarefas.

    - Desabilitar o Configurador de manutenção no Agendador de Tarefas do Windows.

        ou o

    - Download: [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)

      - Depois de baixar o PSEXEC, execute o Windows PowerShell como administrador e digite:

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   Certifique-se de que o dispositivo StorSimple seja atualizado para a [Atualização 3 ou posterior](storsimple-install-update-3.md).

-   Isolar tráfego de iSCSI e de nuvem. Use conexões iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.

-   Verifique se o dispositivo StorSimple é um destino de backup dedicado. Não há suporte para cargas de trabalho mistas, já que essas cargas de trabalho afetam o RTO/RPO.

### <a name="veeam"></a>Veeam

-   O banco de dados do Veeam deve ser local para o servidor e não residir em um volume do StorSimple.

-   Para recuperação de desastres, faça um backup do banco de dados do Veeam em um volume StorSimple.

-   Oferecemos suporte a backups completos e incrementais do Veeam para esta solução. É recomendável que você não use backups diferenciais e sintéticos.

-   Arquivos de dados de backup devem conter apenas dados de um trabalho específico. Por exemplo, não são permitidos acréscimos de mídia em vários trabalhos diferentes.

-   Desabilite a verificação do trabalho. Se necessário, a verificação deve ser agendada após o trabalho de backup mais recente. É importante compreender que esse trabalho afeta sua janela de backup.

-   Desabilite a pré-alocação de mídia.

-   Certifique-se de que o processamento paralelo está habilitado.

-   Desabilite a compactação.

-   Desabilite a eliminação de duplicação no trabalho de backup.

-   Configure a otimização para **Destino LAN**.

-   Habilite **Criar backup ativo completo** (a cada 2 semanas).

-   No repositório de backup, configure **Usar arquivos de backup por VM**.

-   Defina **Usar vários fluxos de carregamento por trabalho** para 8 (o máximo permitido é 16). Ajuste para cima ou para baixo com base na utilização da CPU no dispositivo StorSimple.

## <a name="retention-policies"></a>Políticas de retenção

Uma das políticas de retenção de backup mais usadas é a GFS (Grandfather, Father, and Son - avô, pai e filho). Nessa política, um backup incremental é executado diariamente. Os backups completos são feitos semanal e mensalmente. Essa política resulta em 6 volumes hierárquicos do StorSimple.

-   Um volume contém os backups completos semanais, mensais e anuais.

-   Os outros cinco volumes armazenam backups incrementais diários.

No exemplo a seguir, somos uma rotação de GFS. O exemplo a seguir pressupõe:

-   Serão usados dados compactados ou com eliminação de duplicação.

-   Os backups completos têm 1 TiB cada.

-   Backups incrementais diários têm 500 GiB cada.

-   4 backups semanais mantidos por um mês.

-   12 backups mensais mantidos por um ano.

-   1 backup anual mantido por 10 anos.

Com base nas premissas anteriores, crie um volume em camadas StorSimple de 26 TiB para os backups completos mensais e anuais. Crie um volume em camadas StorSimple de 5 TiB para cada backup diário incremental.

| Retenção de tipo de backup | Tamanho TiB | Multiplicador GFS\*                                       | Capacidade total TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Completo semanal           | 1        | 4                                                      | 4                           |
| Incremental diário     | 0,5      | 20 (ciclos, igual ao número de semanas por mês) | 12 (2 para a cota adicional) |
| Completo mensal          | 1        | 12                                                     | 12                          |
| Completo anual           | 1        | 10                                                     | 10                          |
| Requisito de GFS       |          |                                                        | 38                          |
| Cota adicional      | 4        |                                                        | Requisito total de GFS 42.   |

\*O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender às políticas de backup.

## <a name="configuring-veeam-storage"></a>Configurando o armazenamento Veeam

1.  Vá para as configurações de **Infraestrutura de Backup**. Selecione **Repositórios de Backup** e, em seguida, clique com botão direito e selecione **Adicionar Repositório de Backup**.

    ![Console de gerenciamento Veeam, tela de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

1.  Forneça um **Nome** e **Descrição** para o repositório. Clique em **Avançar>**.

    ![Console de gerenciamento do Veeam, tela nome e descrição](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

1.  Selecione o repositório de backup como **Microsoft Windows Server**. Escolha o Servidor Veeam. Clique em **Avançar>**.

    ![Console de gerenciamento Veeam, selecione o tipo de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

1.  Para especificar o **Local**, navegue e selecione o volume necessário. Define o **limite máximo de tarefas simultâneas para:** para 4. Isso garante que somente 4 discos virtuais serão processados simultaneamente conforme cada VM é processada.
Clique em **Avançado**.

    ![Console de gerenciamento Veeam, selecione o volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


1.  Escolha **Usar arquivos de backup por VM**.

    ![Console de gerenciamento Veeam, configurações de compatibilidade do armazenamento](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

1.  Habilite **serviço vPower NFS no servidor de montagem (recomendado)**.

    ![Console de gerenciamento Veeam, tela de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

1.  Verifique as configurações e prossiga para a próxima página.

    ![Console de gerenciamento Veeam, tela de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Um repositório é adicionado ao servidor Veeam.

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de backup principal

> [!IMPORTANT]
> Se você precisar restaurar dados de um backup em camadas na nuvem, a restauração ocorrerá em velocidades de nuvem.

Na figura a seguir, ilustraremos o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco Sábado Completo e os backups incrementais são mapeados para discos incrementais Segunda a sexta. Todos os backups e as restaurações são feitos de um volume em camadas StorSimple.

![Diagrama lógico de configuração de destino de backup principal ](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Exemplo de agenda StorSimple como destino de backup principal GFS (Grandfather, Father and Son)

| Agendamento de rotação de GFS por quatro semanas, mensal e anual |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Tipo de frequência/ backup   | Completo          | Incremental (dia 1-5)  |
| Semanal (semanas 1 a 4)    | Sábado | Segunda a sexta |
| Mensal     | Sábado  |             |
| Anual      | Sábado  |             |


### <a name="assigning-storsimple-volumes-to-a-veeam-backup-job"></a>Atribuir volumes do StorSimple a um trabalho de backup do Veeam

1.  Crie um trabalho diário com Veeam StorSimple primário no caso de um cenário de destino de backup principal ou NAS/DAS/JBOD no caso de um cenário de destino de backup secundário como destino de backup. Vá para **Backup &#38; Replicação &gt; Backup**. Clique com botão direito e selecione VMware ou Hyper-V de acordo com seu ambiente.

    ![Console de gerenciamento do Veeam, trabalho de novo backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

1.  Forneça um **Nome** e **Descrição** para o trabalho de backup diário.

    ![Console de gerenciamento do Veeam, tela de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

1.  Selecione uma máquina virtual para o backup.

    ![Console de gerenciamento do Veeam, tela de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

1.  Defina um **Proxy de backup** e **Repositório de backup** apropriados. Defina **Pontos de restauração para manter no disco** de acordo com as definições de RPO/RTO para seu ambiente no armazenamento conectado localmente. Clique em Avançado.

    ![Console de gerenciamento do Veeam, tela de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

    Na guia **Backup**, escolha **Incremental**. Certifique-se de que **Criar backups totais sintéticos periodicamente** está desmarcada. Habilite **Criar backups ativos completos periodicamente** e escolha Semanalmente nos **Backups ativos completos** habilitados em todos os sábados.

    ![Console de gerenciamento do Veeam, tela de configurações avançadas de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

    Na guia Armazenamento, certifique-se de que a eliminação de duplicação e compactação estão desabilitadas e que a exclusão e a troca de bloco de arquivo está habilitada. Defina a **Otimização de armazenamento** para **LAN destino** para desempenho equilibrado e eliminação de duplicação.

    ![Console de gerenciamento do Veeam, tela de configurações avançadas de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Para obter detalhes sobre as configurações de eliminação de duplicação e compactação, consulte [Compactação de Dados e Eliminação de Duplicação](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)

1.  Você pode optar por **Habilitar Aplicativo com Reconhecimento de Processamento**.

    ![Console de gerenciamento do Veeam, tela de processamento de novo trabalho de backup de visitante](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

1.  Defina o agendamento para execução uma vez ao dia em uma hora específica de sua escolha.

    ![Console de gerenciamento do Veeam, tela do Agendador de novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

> [!NOTE]
> A restauração ocorre com velocidades de nuvem para os dados de um backup em camadas na nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (que não seja StorSimple) para servir como um cache temporário. Por exemplo, você poderia usar um volume RAID para acomodar a largura de banda, IOs e espaço. É recomendável usar RAID 5, 50 e 10.

Na figura a seguir, mostraremos os volumes locais (para o servidor) de retenção de curto prazo e os de arquivamento para retenção de longo prazo. Nesse caso, todos os backups são executados no volume RAID local (para o servidor). Esses backups são duplicados e arquivados periodicamente em um volume de arquivamento. É importante dimensionar seu volume RAID local (para o servidor) para lidar com os requisitos de capacidade e desempenho da retenção de curto prazo.



| Tipo de backup e retenção                    |Armazenamento configurado| Tamanho (TiB) | Multiplicador GFS | Capacidade total (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Semana 1 (completa e incremental) |Disco local (curto prazo)| 1        | 1              | 1           |
| StorSimple semanas 2 a 4           |Disco StorSimple (longo prazo) | 1        | 4              | 4                   |
| Completo mensal                                 |Disco StorSimple (longo prazo) | 1        | 12             | 12                   |
| Completo anual                               |Disco StorSimple (longo prazo) | 1        | 1              | 1                   |
|Requisito de tamanho de volumes do GFS | |          |                | 18*|

\*A capacidade total inclui 17 TiB de discos do StorSimple e 1 TiB de volume RAID local.

![StorSimple como diagrama lógico de destino de backup secundário ](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

#### <a name="gfs-example-schedule"></a>Exemplo de agenda do GFS

| Agenda semanal, mensal e anual da rotação do GFS|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Semana                                                                     | Completo               | Incremental dia 1        | Incremental dia 2        | Incremental dia 3        | Incremental dia 4        | Incremental dia 5        |
| Semana 1                                                                   | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana 2                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Semana 3                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Semana 4                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Mensal                                                                  | StorSimple mensal |                   |                   |                   |                   |                   |
| Anual                                                                   | StorSimple anual  |                   |                   |                   |                   |                   |


### <a name="assigning-storsimple-volumes-to-a-veeam-copy-job"></a>Atribuir volumes do StorSimple a um trabalho de cópia do Veeam

1.  Inicie o assistente de Novo Trabalho de Cópia de Backup. Vá para **Trabalhos > Cópia de Backup**. Selecione VMware ou Hyper-V de acordo com seu ambiente.

    ![Console de gerenciamento Veeam, tela de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

1.  Especifique o nome do trabalho e a descrição.

    ![Console de gerenciamento Veeam, tela de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

1.  Selecione VMs para processar, selecione de backups e selecione o backup diário criado anteriormente.

    ![Console de gerenciamento Veeam, tela de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

1.  Exclua objetos do trabalho de cópia de backup, se necessário.

1.  Selecione **Repositório de Backup**, defina **Restaurar pontos a manter** e certifique-se de habilitar **manter os seguintes pontos de restauração para fins de arquivamento**. Definir a frequência de backup e, em seguida, clique em **Avançado**.

    ![Console de gerenciamento Veeam, tela de trabalho de nova cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

1.  Especifique as configurações avançadas a seguir:

    -   Na guia **Manutenção**, desabilite o protetor de corrupção de nível de armazenamento.

    -   Na guia **Armazenamento**, certifique-se de que a eliminação de duplicação e compactação estão desabilitadas.

    ![Console de gerenciamento do Veeam, tela de configurações avançadas de novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    ![Console de gerenciamento do Veeam, tela de configurações avançadas de novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

1.  Especifique a transferência de dados como direta.

1.  Defina o agendamento de janela de cópia de backup de acordo com suas necessidades e conclua.


Para obter mais informações, vá para [Criar trabalhos de cópia de backup](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem StorSimple protegem os dados que residem no dispositivo StorSimple. Isso é equivalente ao envio de fitas para um local externo. Em caso de uso do GRS (armazenamento com redundância geográfica do Azure), isso equivale a enviar fitas para vários sites. Se uma restauração de dispositivo fosse necessária em caso de desastre, você poderia colocar outro dispositivo StorSimple on-line e executar um failover. Após o failover, você pode acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.


A seção a seguir ilustra como criar um script curto para acionar e excluir instantâneos de nuvem StorSimple durante o pós-processamento de backup. 

> [!NOTE] 
> Os instantâneos criados manualmente ou por meio de programação não seguem a política de expiração de instantâneos do StorSimple. Esses instantâneos devem ser excluídos manual ou programaticamente.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>Iniciar e excluir instantâneos de nuvem com um script

> [!NOTE] 
> Avalie cuidadosamente as repercussões de conformidade e de retenção de dados antes de excluir um instantâneo do StorSimple. Para saber mais sobre como executar um script pós-backup, consulte a documentação do Veeam.


#### <a name="backup-lifecycle"></a>Ciclo de vida de backup


![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

#### <a name="requirements"></a>Requisitos:

-   O servidor que executa o script deve ter acesso à nuvem do Azure.

-   A conta de usuário deve ter as permissões necessárias.

-   Uma política de backup do StorSimple com os volumes StorSimple associados configurada, mas não habilitada.

-   Nome do recurso de StorSimple, chave de registro, nome do dispositivo e ID da política de backup.

#### <a name="steps"></a>Etapas:

1.  [Instale o Azure PowerShell](/powershell-install-configure/).

2.  [Baixar e importar informações de assinatura e configurações de publicação.](https://msdn.microsoft.com/library/dn385850.aspx)

3.  No portal clássico do Azure, obtenha o nome do recurso e a [chave de registro para o serviço StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

4.  No servidor que executa o script, execute o Windows PowerShell como administrador. Tipo:

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Anote a ID da política de backup.

5.  No bloco de notas, crie um Script do Windows PowerShell e salve-o no mesmo local em que você salvou as configurações de publicação do Azure. Por exemplo: `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`.

    Copie e cole o seguinte trecho de código:

    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
        $SnapshotStartTimeStamp = $Snapshot.CreatedOn
        if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
            $SnapShotInstanceID = $SnapShot.InstanceId
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```

6.  Para adicionar o script ao seu trabalho de backup, edite as opções avançadas do seu trabalho Veeam. 

![Guia de scripts de configurações avançadas de backup Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Recomendamos que você execute a política de backup de instantâneo de nuvem StorSimple ao concluir o trabalho de backup diário como um script de pós-processo. Para saber mais sobre como fazer backup e restaurar o ambiente de aplicativo de backup para atender a RPO/RTO, consulte o arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como origem de restauração
==============================

As restaurações de um dispositivo StorSimple funcionam como qualquer dispositivo de armazenamento em bloco. Ao restaurar os dados que estão em camadas na nuvem, a restauração ocorre em velocidades de nuvem. Para dados locais, a restauração ocorre na velocidade do disco local do dispositivo.

O Veeam permite uma rápida recuperação granular em nível de arquivo por meio do StorSimple usando os Explorers internos no console do Veeam. Use os pesquisadores Veeam para recuperar itens individuais como itens do SharePoint, objetos do Active Directory ou mensagens de email dos backups. A recuperação pode ser feita sem interrupções de VMs locais. Você também pode realizar a recuperação point-in-time para Microsoft SQL e bancos de dados Oracle. O Veeam e StorSimple tornam o processo de recuperação em nível de item do Azure rápido e fácil. Para obter informações sobre como executar uma restauração, consulte a documentação do Veeam.


- [https://www.veeam.com/microsoft-exchange-recovery.html](https://www.veeam.com/microsoft-exchange-recovery.html)

- [https://www.veeam.com/microsoft-active-directory-explorer.html](https://www.veeam.com/microsoft-active-directory-explorer.html)

- [https://www.veeam.com/microsoft-sql-server-explorer.html](https://www.veeam.com/microsoft-sql-server-explorer.html)

- [https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)

- [https://www.veeam.com/oracle-backup-recovery-explorer.html](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o StorSimple Cloud Appliance não tem suporte como um destino de restauração.

Um desastre pode ocorrer devido a vários fatores. A tabela a seguir lista cenários comuns de recuperação de desastre.


| Cenário                                                                    | Impacto                                             | Como recuperar                                                                                                                                                                               | Observações                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Falha do dispositivo StorSimple.                                               | As operações de backup e restauração foram interrompidas. | Substitua o dispositivo com falha e execute [Failover e recuperação de desastre do StorSimple](storsimple-device-failover-disaster-recovery.md) | Se uma restauração é executada imediatamente após a recuperação do dispositivo, os conjuntos de trabalho totais são recuperados da nuvem para o novo dispositivo. Em função disso, todas as operações ocorrem em velocidades de nuvem. Além disso, a nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada de nuvem para a camada local do dispositivo. Isso torna tudo ainda mais demorado.                                 |
| Falha do servidor Veeam.                                                     | As operações de backup e restauração foram interrompidas. | Recrie o servidor de backup e execute a restauração do banco de dados conforme detalhado em [Centro de Ajuda Veeam (Documentação Técnica)](https://www.veeam.com/documentation-guides-datasheets.html)     | O servidor Veeam precisa ser recriado ou restaurado no local de DR. Restaure o banco de dados para o ponto mais recente. Se o banco de dados restaurado do Veeam não estiver sincronizado com os trabalhos de backup mais recentes, será necessário fazer a indexação e a catalogação. Essa verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada de nuvem para a camada local do dispositivo. Isso torna tudo ainda mais demorado. |
| Falha do site que resulta na perda do servidor de Backup e do StorSimple. | As operações de backup e restauração foram interrompidas. | Restaure o StorSimple primeiro e, depois, o Veeam.                                                                                                                                                  | Restaure o StorSimple primeiro e, depois, o Veeam. Se houver a necessidade de realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho totais são recuperados da nuvem para o novo dispositivo. Em função disso, todas as operações ocorrem em velocidades de nuvem.                                                                                                                                                                            |


## <a name="references"></a>Referências

Os documentos a seguir foram mencionados neste artigo:

- [Instalação de MPIO do StorSimple](storsimple-configure-mpio-windows-server.md)

- [Cenários de armazenamento: provisionamento dinâmico](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Usando unidades GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Habilitar e configurar cópias de sombra de pastas compartilhadas](http://technet.microsoft.com/library/cc771893.aspx)



<!--HONumber=Dec16_HO2-->


