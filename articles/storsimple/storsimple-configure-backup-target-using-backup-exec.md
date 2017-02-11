---
title: Configurar o StorSimple com Veritas BackupExec | Microsoft Docs
description: "Descreve a configuração de Destino de Backup do StorSimple com o Veritas BackupExec."
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
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: b7c6a9ffb2c598048bf0547420b1e7afac2c3e74
ms.openlocfilehash: 7a2fa4ccac6b2eda935477034c5f58a0ed90da37

---

# <a name="configure-storsimple-with-veritas-backup-exec8482"></a>Configurar o StorSimple com Veritas Backup Exec&#8482;

## <a name="overview"></a>Visão geral

O Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida que resolve as complexidades do crescimento exponencial de dados. Essa solução usa uma conta de armazenamento do Azure como uma extensão da solução local e dispõe os dados em camadas automaticamente no armazenamento local e no armazenamento em nuvem.

Este artigo discute a integração do StorSimple com o Veritas Backup Exec e as práticas recomendadas para a integração de ambas as soluções. Também apresentamos recomendações sobre como configurar o Veritas Backup Exec para se integrar melhor ao StorSimple. Consulte as práticas recomendadas, os arquitetos de backup e os administradores da Veritas sobre como configurar melhor o Veritas Backup Exec para atender às suas necessidades de backup e aos SLAs.

Este artigo ilustra os principais conceitos e as etapas de configuração, mas não é um guia passo a passo de configuração ou instalação. O artigo pressupõe que a infraestrutura e os componentes básicos estão funcionando e prontos para dar suporte aos conceitos que descrevemos.

## <a name="why-storsimple-as-a-backup-target"></a>Por que o StorSimple como um destino de backup?

O StorSimple é um ótimo destino de backup pelos seguintes motivos:

-   Ele oferece armazenamento local padrão para uso por aplicativos de backup sem alterações, o que o torna um destino de backup rápido. O StorSimple também está disponível para a restauração rápida de backups recentes.

-   Sua disposição em camadas na nuvem está totalmente integrada ao armazenamento em nuvem econômico do Microsoft Azure.

-   Ele oferece armazenamento externo para recuperação de desastre automaticamente.


## <a name="target-audience"></a>Público-alvo

O público deste artigo inclui administradores de backup, administradores de armazenamento e arquitetos de armazenamento que conheçam os conceitos de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e Veritas Backup Exec.

## <a name="supported-versions"></a>Versões com suporte

Para obter uma lista completa de versões com suporte, vá para:

-   [Backup Exec versões 16 e posteriores](http://backupexec.com/compatibility).

-   [StorSimple Atualização 3 e posteriores](storsimple-overview.md#storsimple-workload-summary).

## <a name="key-concepts"></a>Principais conceitos

Assim como acontece com qualquer solução de armazenamento, o sucesso depende de se fazer uma avaliação cuidadosa do desempenho do armazenamento da solução, dos SLAs, da taxa de alteração e das necessidades de aumento da capacidade. A ideia principal é que, com a introdução de uma camada de nuvem, seus horários de acesso e produtividade na nuvem terão um papel fundamental na capacidade do StorSimple de fazer seu trabalho.

O StorSimple foi projetado para oferecer armazenamento a aplicativos que operam em um conjunto de dados de trabalho bem definido (dados mais acessados). Nesse modelo, o conjunto de dados de trabalho é armazenado nas camadas locais e o conjunto de dados que não é de trabalho/menos acessado/arquivado restante é colocado em camadas na nuvem. Esse modelo é representado na figura a seguir. A linha verde quase reta representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todas as camadas. O espaço entre a linha verde reta e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

**Disposição em camadas do StorSimple**
![Diagrama da disposição em camadas do Storsimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

Com essa arquitetura em mente, você descobrirá que o StorSimple é ideal para operar como um destino de backup. O StorSimple permite que você:

-   Realize restaurações mais frequentes do conjunto de dados de trabalho local.

-   Utilize a nuvem em uma recuperação de desastre externa e para dados mais antigos, para os quais as restaurações são menos frequentes.

## <a name="storsimple-benefits"></a>Benefícios do StorSimple

O StorSimple oferece uma solução local que está totalmente integrada ao Microsoft Azure, aproveitando as vantagens do acesso contínuo ao armazenamento local e em nuvem.

O StorSimple usa uma disposição em camadas automática entre o dispositivo local, que contém armazenamentos SSD (dispositivos de estado sólido), SAS (SCSI) anexado em série e Armazenamento do Azure. A disposição em camadas automática mantém os dados acessados com frequência no local, nas camadas de SSD e SAS e move os dados raramente acessados para o Armazenamento do Azure.

O StorSimple oferece os seguintes benefícios:

-   Algoritmos de eliminação de duplicação e compactação exclusivos que usam a nuvem para alcançar níveis de eliminação de duplicação sem precedentes

-   Alta disponibilidade

-   Replicação geográfica aproveitando a replicação geográfica do Azure

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


| Capacidade de armazenamento       | 8100          | 8600            |
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

![StorSimple como um diagrama lógico de destino de backup principal](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino principal

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.

2.  O servidor de backup grava os dados nos volumes hierárquicos do StorSimple.

3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.

4.  O script de instantâneo dispara o gerenciamento de instantâneo de nuvem do StorSimple (start-delete).

5.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

###  <a name="primary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino principal

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.

2.  O agente de backup recebe os dados do servidor de Backup.

3.  O servidor de backup conclui o trabalho de restauração.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

Nesse cenário, os volumes do StorSimple são usados principalmente para retenção de longo prazo ou arquivamento.

A figura a seguir ilustra a arquitetura em que os backups e restaurações iniciais são destinados a um volume de alto desempenho. Esses backups são copiados e arquivados em um volume em camadas do StorSimple em determinada agenda.

É importante dimensionar o volume de alto desempenho com espaço e desempenho suficientes para lidar com os requisitos de capacidade e desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino secundário

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.

2.  O servidor de backup grava dados em armazenamento de alto desempenho.

3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.

4.  O servidor de backup copia os backups no StorSimple com base em uma política de retenção.

5.  O script de instantâneo dispara o gerenciamento de instantâneo de nuvem do StorSimple (start-delete).

6.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino secundário

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.

2.  O agente de backup recebe os dados do servidor de backup.

3.  O servidor de backup conclui o trabalho de restauração.

## <a name="deploy-the-solution"></a>Implantar a solução

A implantação dessa solução consiste em três etapas: preparar a infraestrutura de rede, implantar seu dispositivo StorSimple como um destino de backup e implantar o Veritas Backup Exec. Cada uma das etapas acima será discutida nas seções a seguir.

### <a name="configure-the-network"></a>Configurar a rede

O StorSimple, como uma solução integrada com a nuvem do Azure, requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada em operações como instantâneos de nuvem, gerenciamento, transferência de metadados e disposição de dados menos acessados em camadas no armazenamento em nuvem do Azure.

Para que a solução tenha o desempenho ideal, recomendamos seguir as práticas recomendadas de rede abaixo:

-   O link que conecta as camadas do StorSimple ao Azure deve atender aos seus requisitos de largura de banda pela aplicação adequada da QoS (qualidade de serviço) às opções de infraestrutura para corresponder aos SLAs de RPO/RTO.

-   As latências máximas de acesso ao Armazenamento de Blobs do Azure devem estar no intervalo de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para ser orientado passo a passo na implantação do StorSimple, vá para [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veritas-backup-exec"></a>Implantar o Veritas Backup Exec

Para práticas recomendadas de instalação do Veritas Backup Exec, vá para [Práticas recomendadas para a instalação do Backup Exec](https://www.veritas.com/support/en_US/article.000068207).

## <a name="configure-the-solution"></a>Configurar a solução

Nesta seção, demonstraremos alguns exemplos de configuração. Os exemplos/recomendações a seguir ilustram a implementação mais básica e fundamental. Essa implementação pode não se aplicar diretamente aos seus requisitos específicos de backup.

### <a name="configure-storsimple"></a>Configurar o StorSimple

| Tarefas de implantação do StorSimple                                                                                                                 | Comentários adicionais                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Implantação do seu dispositivo StorSimple local.                                                                                                | Versão com suporte: atualização 3 e posterior.                                                                                                                                                                                                                                                                 |
| Habilitar o modo de destino de backup.                                                                                                                   | Use os comandos a seguir para habilitar/desabilitar e obter o status. Para obter mais informações, consulte [conectar -se remotamente a um StorSimple](storsimple-remote-connect.md).</br> Habilitar o modo de backup:`Set-HCSBackupApplianceMode -enable`</br>  Desabilitar o modo de backup:`Set-HCSBackupApplianceMode -disable`</br> Estado atual das configurações de modo de backup`Get-HCSBackupApplianceMode` |
| Crie um contêiner de volume comum para o seu volume que armazena os dados de backup.   Todos os dados de um contêiner de volume passam por eliminação de duplicação. | Contêineres de volume do StorSimple definem domínios de eliminação de duplicação.                                                                                                                                                                                                                                             |
| Criar volumes do StorSimple                                                                                                                 | Crie volumes com tamanhos o mais próximo possível do uso antecipado, já que o tamanho do volume afeta o tempo de duração do instantâneo de nuvem. Para saber mais sobre como dimensionar um volume, confira [Políticas de retenção](#retention-policies).</br> </br> Use os volumes em camadas do StorSimple e selecione **Use este volume para dados de arquivamento acessados com menos frequência**. </br> Volumes afixados localmente apenas não têm suporte.|
| Crie uma política de backup do StorSimple exclusiva para todos os volumes de destino de backup.                                                               | Uma política de backup do StorSimple define o grupo de consistência do volume.                                                                                                                                                                                                                                       |
| Desabilitar a agenda como instantâneos.                                                                                                    | Os instantâneos são disparados como uma operação de pós-processamento.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |




### <a name="configure-host-backup-server-storage"></a>Configurar o armazenamento do servidor de backup do host

Verifique se o armazenamento do servidor de backup do host está configurado de acordo com as diretrizes a seguir.  

- Não há suporte para volumes estendidos (criados pelo Gerenciador de Disco do Windows).
- Formate os volumes usando o NTFS com tamanho de alocação de 64 KB.
- Mapeie os volumes do StorSimple diretamente ao servidor “Veeam”.
    - Use iSCSI no caso de servidores físicos.
    - Use discos de passagem para servidores virtuais.


## <a name="best-practices-for-storsimple-and-veritas-backup-exec"></a>Práticas recomendadas para StorSimple e Veritas Backup Exec

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

-   Verifique se o dispositivo StorSimple é um destino de backup dedicado. Não há suporte para cargas de trabalho mistas, já que elas afetam o RTO/RPO.

### <a name="veritas-backup-exec"></a>VERITAS Backup Exec

-   O VERITAS Backup Exec deve ser instalado em uma unidade local do servidor e não em um volume StorSimple.

-   Defina as **operações de gravação simultâneas** do Veritas Backup Exec Storage como valor máximo permitido.

    -   O **tamanho de bloco e buffer** do Veritas Backup Exec Storage deve ser definido como 512 KB.

    -   A **leitura e gravação em buffer** do Armazenamento do Veritas Backup Exec deve ser habilitada.

-   Há suporte para backups completos e incrementais do Veritas Backup Exec, mas os backups sintéticos e diferenciais não são recomendados.

-   Arquivos de dados de backup devem conter apenas dados de um trabalho específico. Por exemplo, não são permitidos acréscimos de mídia em vários trabalhos diferentes.

-   Desabilite a verificação do trabalho. Se necessário, a verificação deve ser agendada após o trabalho de backup mais recente. É importante compreender que esse trabalho afeta sua janela de backup.

-   Em **Armazenamento > Seu Disco > Detalhes > Propriedades**, desabilite **Alocar espaço em disco previamente**.

Consulte a documentação do Veritas Backup Exec para ver as configurações do Veritas Backup Exec mais recentes e práticas recomendadas para implementar esses requisitos visitando [www.veritas.com](https://www.veritas.com).

## <a name="retention-policies"></a>Políticas de retenção

Uma das políticas de retenção de backup mais usadas é a GFS (Grandfather, Father, and Son - avô, pai e filho). Nessa política, um backup incremental é executado diariamente. Os backups completos são feitos semanal e mensalmente. Essa política resulta em 6 volumes hierárquicos do StorSimple.

-   Um volume contém os backups completos semanais, mensais e anuais.

-   Os outros cinco volumes armazenam backups incrementais diários.

No exemplo a seguir, somos uma rotação de GFS. O exemplo pressupõe o seguinte:

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
| Cota adicional      | 4        |                                                        | Requisito total de GFS 42   |

\*O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender às políticas de backup.

## <a name="configure-veritas-backup-exec-storage"></a>Configurar o armazenamento do Veritas Backup Exec

1.  No console de gerenciamento do Veritas Backup Exec, selecione **Armazenamento &gt; Configurar Armazenamento &gt; Armazenamento Baseado em Disco**. Clique em **Avançar&gt;**.

    ![Console de gerenciamento do VERITAS Backup Exec, tela Configurar armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

1.  Clique em **Armazenamento em Disco** e prossiga para a próxima página.

    ![Console de gerenciamento do VERITAS Backup Exec, tela Selecionar armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

1.  Insira um nome representativo, por exemplo, Sábado Completo e uma descrição de acompanhamento. Prossiga para a próxima página.

    ![Console de gerenciamento do VERITAS Backup Exec, tela Nome e descrição](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

1.  Selecione o disco apropriado e clique em **Avançar&gt;**. Por exemplo, selecionamos D:.

    ![Console de gerenciamento do VERITAS Backup Exec, tela Seleção do disco de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

1.  Aumente o número de operações de gravação para 16 e clique em **Avançar&gt;**.

    ![Console de gerenciamento do Veritas Backup Exec, tela Configurações de operações de gravação simultâneas ](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

1.  Examine as configurações e clique em **Concluir**.

    ![Console de gerenciamento do VERITAS Backup Exec, tela Resumo de configuração do armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

1.  No final de cada atribuição, altere as configurações do dispositivo de armazenamento de acordo com as configurações indicadas na lista de práticas recomendadas.

    ![Console de gerenciamento do VERITAS Backup Exec, tela Configurações do dispositivo de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

1.  Repita as etapas anteriores até concluir a atribuição dos volumes do StorSimple ao Veritas Backup Exec.

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de backup principal

> [!NOTE]
> Lembre-se de que se você precisar restaurar dados de um backup em camadas na nuvem, a restauração ocorrerá em velocidades de nuvem.

Na figura a seguir, ilustraremos o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco Sábado Completo e os backups incrementais são mapeados para discos incrementais Segunda a sexta. Todos os backups e as restaurações são feitos de um volume em camadas StorSimple.

![Diagrama lógico de configuração de destino de backup principal ](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Exemplo de agenda StorSimple como destino de backup principal GFS (Grandfather, Father and Son)

| Agendamento de rotação de GFS por quatro semanas, mensal e anual |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Tipo de frequência/ backup   | Completo          | Incremental (dia 1-5)  |
| Semanal (semanas 1 a 4)    | Sábado | Segunda a sexta | 
| Mensal     | Sábado  |             |
| Anual      | Sábado  |             |


### <a name="assigning-storsimple-volumes-to-a-veritas-backup-exec-backup-job"></a>Atribuir volumes do StorSimple a um trabalho de backup do Veritas Backup Exec.

A sequência a seguir pressupõe que o Backup Exec e o host de destino estão configurados de acordo com as diretrizes do agente do Backup Exec.

1.  No console de gerenciamento do Veritas Backup Exec, vá para **Host &gt; Backup &gt; Backup em Disco &gt;**.

    ![No console de gerenciamento do Veritas Backup Exec, vá para Host, Backup, Backup em disco](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

1.  Na janela Propriedades de Definição de Backup, selecione **Editar** (em Backup).

    ![Selecionar Editar na janela Propriedades de Definição de Backup](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

1.  Configure os backups completos e incrementais para que eles atendam aos requisitos de RPO/RTO e estejam de acordo com as práticas recomendadas do Veritas.

2.  Na janela de opções de Backup, selecione **Armazenamento**.

    ![Guia Armazenamento de opções de backup](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

1.  Atribua volumes do StorSimple correspondentes ao o agendamento de backup.

    > [!NOTE]
    > **Compactação** e **Tipo de criptografia** são definidos como **Nenhum**.

2.  Em **Verificar**, selecione a opção **Não verificar os dados para este trabalho**, pois isso pode afetar a disposição em camadas do StorSimple.

    > [!NOTE]
    > A desfragmentação, a indexação e a verificação em segundo plano afetam negativamente a disposição em camadas do StorSimple.

    ![Configurações de verificação das Opções de Backup](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

1.  Depois de configurar o restante das suas opções de backup para atender às suas necessidades, selecione **OK** para concluir.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

> [!NOTE]
> Lembre-se de que se você precisar restaurar dados de um backup em camadas na nuvem, a restauração ocorrerá em velocidades de nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (que não seja StorSimple) para servir como um cache temporário. Por exemplo, você poderia usar um volume RAID para acomodar largura de banda, E/S e espaço. Recomendamos o uso de RAID 5, 50 e 10.

Na figura a seguir, mostraremos os volumes locais (para o servidor) de retenção de curto prazo e os de arquivamento para retenção de longo prazo. Nesse caso, todos os backups são executados no volume RAID local (para o servidor). Esses backups são duplicados e arquivados periodicamente em um volume de arquivamento. É importante dimensionar seu volume RAID local (para o servidor) para lidar com os requisitos de capacidade e desempenho da retenção de curto prazo.

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS de destino de backup secundário

![StorSimple como diagrama lógico de destino de backup secundário ](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

A tabela a seguir ilustra como os backups devem ser configurados para execução no local e nos discos do StorSimple e inclui os requisitos de capacidade total e individual.

#### <a name="backup-configuration-and-capacity-requirements"></a>Configuração de backup e requisitos de capacidade

| Tipo de backup e retenção                    |Armazenamento configurado| Tamanho (TiB) | Multiplicador GFS | Capacidade total (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Semana 1 (completa e incremental) |Disco local (curto prazo)| 1        | 1              | 1           |
| StorSimple semanas 2 a 4           |Disco StorSimple (longo prazo) | 1        | 4              | 4                   |
| Completo mensal                                 |Disco StorSimple (longo prazo) | 1        | 12             | 12                   |
| Completo anual                               |Disco StorSimple (longo prazo) | 1        | 1              | 1                   |
|Requisito de tamanho de volumes do GFS | |          |                | 18*|

\*a capacidade total inclui 17 TiB de discos do StorSimple e 1 TiB de volume RAID local.


#### <a name="gfs-example-schedule"></a>Exemplo de agenda do GFS

|Agenda semanal, mensal e anual da rotação do GFS|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Frequência                                                                     | Completo               | Incremental dia 1        | Incremental dia 2        | Incremental dia 3        | Incremental dia 4        | Incremental dia 5        |
| Semana 1                                                                   | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana 2                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Semana 3                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Semana 4                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Mensal                                                                  | StorSimple mensal |                   |                   |                   |                   |                   |
| Anual                                                                   | StorSimple anual  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-backup-exec-archivededuplication-job"></a>Atribuir volumes StorSimple a trabalhos de arquivamento/eliminação de duplicação do Backup Exec

1.  Depois que o trabalho de backup inicial estiver configurado para usar o volume RAID como destino principal de backup, vá para o console de gerenciamento do Veritas Backup Exec. Selecione o trabalho que você deseja arquivar em um volume StorSimple, clique com o botão direito do mouse e selecione **Propriedades de Definição de Backup**. Clique em **Editar**.

    ![Guia Propriedades de definição de backup do console do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

1.  Selecione **Adicionar Estágio** e, na lista suspensa, selecione **Duplicar em Disco**. Clique em **Editar**.

    ![Adicionar estágio na definição de backup do console do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

2.  Em **Opções Duplicadas**, selecione a **Origem** e a **Agenda** apropriadas.

    ![Duplicar Opções na definição de backup do console do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

1.  Na lista suspensa **Armazenamento**, selecione o volume StorSimple onde você deseja que o trabalho de arquivamento armazene os dados. Nesse caso, a origem é definida como **Completo** e o destino é definido como o volume de arquivamento completo StorSimple.

    ![Duplicar Opções na definição de backup do console do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

1.  Acesse **Verificar** e selecione **Não verificar os dados para este trabalho**.

    ![Duplicar Opções na definição de backup do console do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

1.  Clique em **OK**.

    ![Duplicar Opções na definição de backup do console do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

1.  Na coluna **Backup**, adicione um novo estágio. Escolha a origem como incremental e o destino como o volume StorSimple onde o trabalho de backup incremental está arquivado. Repita as etapas anteriores.

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem StorSimple protegem os dados que residem no dispositivo StorSimple. Isso equivale a enviar fitas para uma instalação externa e, em caso de uso do GRS (armazenamento com redundância geográfica do Azure), a enviar fitas para vários sites. Se uma restauração de dispositivo fosse necessária em caso de desastre, você poderia colocar outro dispositivo StorSimple online e executar um failover. Após o failover, você poderia acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.

A seção a seguir ilustra como criar um script curto para acionar e excluir instantâneos de nuvem StorSimple durante o pós-processamento de backup.

> [!NOTE]
> Os instantâneos criados manualmente ou por meio de programação não seguem a política de expiração de instantâneos do StorSimple. Eles devem ser excluídos manual ou programaticamente.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>Iniciar e excluir instantâneos de nuvem com um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e de retenção de dados antes de excluir um instantâneo do StorSimple. Para saber mais sobre como executar um script pós-backup, consulte a documentação do Veritas Backup Exec.

#### <a name="backup-lifecycle"></a>Ciclo de vida de backup

![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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

5.  No Bloco de Notas, crie um novo Script do Windows PowerShell e salve-o no mesmo local em que você salvou as configurações de publicação do Azure. Por exemplo: `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`.

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
        Write-Host "This snapshotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
        Write-Host "Instance ID " $SnapShotInstanceID
        Write-Host "This snpashotdate is older and needs to be deleted"
        Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
        Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }   
    ```

1.  Adicione o script ao seu trabalho de backup no Veritas Backup Exec editando seus comandos de pré-publicação das opções do trabalho do Veritas Backup Exec.

    ![guia Pré/Pós-comandos das opções de backup do console do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Recomendamos que você execute a política de backup de instantâneo de nuvem StorSimple ao concluir o trabalho de backup diário como um script de pós-processo. Para saber mais sobre como fazer backup e restaurar o ambiente de aplicativo de backup para atender a RPO/RTO, consulte o arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como origem de restauração

As restaurações de um StorSimple funcionam mais ou menos como qualquer dispositivo de armazenamento em bloco. Ao restaurar os dados que estão em camadas na nuvem, a restauração ocorre em velocidades de nuvem. Para dados locais, a restauração ocorre na velocidade do disco local do dispositivo. Para saber mais sobre como executar uma restauração, consulte a documentação do Veritas Backup Exec e as práticas recomendadas do Veritas Backup Exec.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o StorSimple Cloud Appliance não tem suporte como um destino de restauração.

Um desastre pode ocorrer devido a vários fatores. A tabela a seguir lista cenários comuns de recuperação de desastre.

| Cenário                                                                    | Impacto                                             | Como recuperar                                                                                                                                                                               | Observações                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Falha do dispositivo StorSimple.                                                | As operações de backup e restauração foram interrompidas. | Substitua o dispositivo com falha e execute [Failover e recuperação de desastre do StorSimple](storsimple-device-failover-disaster-recovery.md). | Se uma restauração é executada imediatamente após a recuperação do dispositivo, os conjuntos de trabalho totais são recuperados da nuvem para o novo dispositivo. Em função disso, todas as operações ocorrem em velocidades de nuvem. Além disso, a nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada de nuvem para a camada local do dispositivo. Isso torna tudo ainda mais demorado.                                                               |
| Falha do servidor Veritas Backup Exec.                                       | As operações de backup e restauração foram interrompidas. | Recrie o servidor de backup e execute a restauração do banco de dados conforme detalhado em [Como fazer backup e restauração manual do BEDB (banco de dados do Backup Exec)](http://www.veritas.com/docs/000041083).            | O servidor Veritas Backup Exec precisa ser recriado ou restaurado no local de recuperação de desastre. O banco de dados precisa ser restaurado para o ponto mais recente. Se o banco de dados restaurado do Veritas Backup Exec não está sincronizado com os trabalhos de backup mais recentes, é necessário fazer a indexação e a catalogação. Essa verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada de nuvem para a camada local do dispositivo. Isso torna tudo ainda mais demorado. |
| Falha do site que resulta na perda do servidor de Backup e do StorSimple. | As operações de backup e restauração foram interrompidas. | Restaure o StorSimple primeiro para depois restaurar o Veritas Backup Exec.                                                                                                                                   | Restaure o StorSimple primeiro para depois restaurar o Veritas Backup Exec.                                                                Se houver a necessidade de realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho totais são recuperados da nuvem para o novo dispositivo. Em função disso, todas as operações ocorrem em velocidades de nuvem.|


## <a name="references"></a>Referências

Os documentos a seguir foram mencionados neste artigo:

- [Instalação de MPIO do StorSimple](storsimple-configure-mpio-windows-server.md)

- [Cenários de armazenamento: provisionamento dinâmico](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Usando unidades GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Habilitar e configurar cópias de sombra de pastas compartilhadas](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Próximas etapas

Saiba mais:

- Sobre como [restaurar de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Sobre como executar [recuperação de desastre e failover de dispositivo](storsimple-device-failover-disaster-recovery.md).


<!--HONumber=Dec16_HO2-->


