---
title: Configure o StorSimple com o Veritas NetBackup | Microsoft Docs
description: "Descreve a configuração de destino de Backup do StorSimple com o Veritas NetBackup."
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
ms.sourcegitcommit: 45fc96674d52a1e1273eed390195c59889b86a38
ms.openlocfilehash: 50c4be545f3b852d99968548784ffdff048d5355

---

# <a name="configure-storsimple-with-veritas-netbackup8482"></a>Configure o StorSimple com o Veritas NetBackup&#8482;

## <a name="overview"></a>Visão geral

O Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida que atende às complexidades do crescimento exponencial de dados, usando uma conta de armazenamento do Azure como uma extensão da solução local e dos dados postos em camadas automaticamente no armazenamento local e no armazenamento em nuvem.

Este artigo discute a integração do StorSimple com o Veritas NetBackup e as práticas recomendadas para a integração de ambas as soluções. Também apresentamos recomendações sobre como configurar o Veritas NetBackup para se integrar melhor ao StorSimple. Consultamos as melhores práticas, os arquitetos de backup e os administradores da Veritas sobre como configurar melhor o Veritas NetBackup para atender às suas necessidades de backup e aos SLAs.


Este artigo ilustra os principais conceitos e as etapas de configuração, mas não é um guia passo a passo de configuração ou instalação. O artigo pressupõe que a infraestrutura e os componentes básicos estão funcionando e prontos para dar suporte aos conceitos que descrevemos.

## <a name="why-storsimple-as-a-backup-target"></a>Por que o StorSimple como um destino de backup?

O StorSimple é um ótimo destino de backup pelos seguintes motivos:

-   Ele oferece armazenamento local padrão para uso por aplicativos de backup sem alterações, o que o torna um destino de backup rápido. O StorSimple também está disponível para a restauração rápida de backups recentes.

-   Sua disposição em camadas na nuvem está totalmente integrada a uma conta de armazenamento em nuvem para usar o armazenamento econômico do Microsoft Azure.

-   Ele oferece armazenamento externo para recuperação de desastre automaticamente.


## <a name="target-audience"></a>Público-alvo

O público deste artigo inclui administradores de backup, administradores de armazenamento e arquitetos de armazenamento que conheçam os conceitos de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e NetBackup.

## <a name="supported-versions"></a>Versões com suporte

-   NetBackup 7.7.x e acima.

-   [StorSimple Atualização 3 e acima](storsimple-overview.md#storsimple-workload-summary).

## <a name="key-concepts"></a>Principais conceitos

Assim como acontece com qualquer solução de armazenamento, o sucesso depende de se fazer uma avaliação cuidadosa do desempenho do armazenamento da solução, dos SLAs, da taxa de alteração e das necessidades de aumento da capacidade. A ideia principal é que, com a introdução de uma camada de nuvem, seus horários de acesso e produtividade na nuvem terão um papel fundamental na capacidade do StorSimple de fazer seu trabalho.

O StorSimple foi projetado para oferecer armazenamento a aplicativos que operam em um conjunto de dados de trabalho bem definido (dados mais acessados). Nesse modelo, o conjunto de dados de trabalho é armazenado nas camadas locais e o conjunto de dados que não é de trabalho/menos acessado/arquivado restante é colocado em camadas na nuvem. Esse modelo é representado na figura a seguir. A linha verde quase reta representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todas as camadas. O espaço entre a linha verde reta e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

**Disposição em camadas do StorSimple**
![Diagrama da disposição em camadas do Storsimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)


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

Para saber mais sobre o StorSimple, consulte [StorSimple série 8000: solução de armazenamento de nuvem híbrida](storsimple-overview.md) e examine as [especificações técnicas da série StorSimple 8000](storsimple-technical-specifications-and-compliance.md).


> [!IMPORTANT] 
> Só existe suporte para o dispositivo StorSimple como destino de backup no StorSimple 8000 Atualização 3 ou posterior.


## <a name="architecture-overview"></a>Visão geral da arquitetura

As tabelas a seguir contêm as diretrizes iniciais do modelo à arquitetura do dispositivo.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>Capacidades de armazenamento local e em nuvem do StorSimple


| Capacidade de armazenamento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacidade de armazenamento local | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacidade de armazenamento em nuvem | &gt; 200 TiB\* | &gt; 500 TiB\* |

\*O tamanho de armazenamento não assume nenhuma eliminação de duplicação ou compactação.

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>Capacidades de StorSimple para os backups primários e secundários


| Cenário de backup  | Capacidade de armazenamento local                                         | Capacidade de armazenamento em nuvem                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| Backup principal   | Backups recentes armazenados no armazenamento local para rápida recuperação (RPO). | O histórico de backup (RPO) se encaixa na capacidade da nuvem. |
| Backup secundário | Uma cópia secundária dos dados de backup pode ser armazenada na capacidade da nuvem.  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de backup principal

Nesse cenário, são apresentados os volumes do StorSimple para o aplicativo de backup como o único repositório para backups. A figura a seguir ilustra a arquitetura da solução em que todos os backups usam volumes em camadas do StorSimple para backups e restaurações.

![StorSimple como um diagrama lógico de destino de backup principal](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino principal

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.

2.  O servidor de backup grava os dados nos volumes em camadas do StorSimple.

3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.

4.  O script de instantâneo dispara o gerenciamento de instantâneo de nuvem do StorSimple (start-delete).

5.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

###  <a name="primary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino principal

1.  O servidor de backup começa a restaurar os dados pertinentes do repositório de armazenamento.

2.  O agente de backup recebe os dados do servidor de backup.

3.  O servidor de backup conclui o trabalho de restauração.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

Nesse cenário, os volumes do StorSimple são usados principalmente para retenção de longo prazo ou arquivamento.

A figura a seguir ilustra a arquitetura em que os backups e restaurações iniciais são destinados a um volume de alto desempenho. Esses backups são copiados e arquivados em um volume em camadas do StorSimple em determinada agenda.

É importante dimensionar o volume de alto desempenho com espaço e desempenho suficientes para lidar com os requisitos de capacidade e desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Etapas de lógica de backup de destino secundário

1.  O servidor de backup entra em contato com o agente de backup e este transmite dados para o servidor de backup.

2.  O servidor de backup grava dados em armazenamento de alto desempenho.

3.  O servidor de backup atualiza o banco de dados do catálogo e conclui o trabalho de backup.

4.  O servidor de backup copia os backups no StorSimple com base em uma política de retenção.

5.  O script de instantâneo dispara o gerenciamento de instantâneo de nuvem do StorSimple (start-delete).

6.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino secundário

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.

2.  O agente de backup recebe os dados do servidor de backup.

3.  O servidor de backup conclui o trabalho de restauração.

## <a name="deploy-the-solution"></a>Implantar a solução

A implantação dessa solução consiste em três etapas: preparar a infraestrutura de rede, implantar seu dispositivo StorSimple como um destino de backup e implantar o Veritas NetBackup. Cada uma das etapas acima será discutida nas seções a seguir.

### <a name="configure-the-network"></a>Configurar a rede

O StorSimple, como uma solução integrada com a nuvem do Azure, requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada em operações como instantâneos de nuvem, gerenciamento, transferência de metadados e disposição de dados menos acessados em camadas no armazenamento em nuvem do Azure.

Para que a solução tenha o desempenho ideal, recomendamos seguir as práticas recomendadas de rede abaixo:

-   O link que conecta as camadas do StorSimple ao Azure deve atender aos seus requisitos de largura de banda pela aplicação adequada da QoS (qualidade de serviço) às opções de infraestrutura para corresponder aos SLAs de RPO/RTO.

-   As latências máximas de acesso ao Armazenamento de Blobs do Azure devem estar no intervalo de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para ser orientado passo a passo na implantação do StorSimple, acesse [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Implantar o NetBackup

Um guia de implantação do passo a passo do NetBackup 7.7.x pode ser encontrado em [Documentação do NetBackup 7.7.x ](https://www.veritas.com/support/article.000094423)

## <a name="configure-the-solution"></a>Configurar a solução

Nesta seção, demonstraremos alguns exemplos de configuração. Os exemplos/recomendações a seguir ilustram a implementação mais básica e fundamental. Essa implementação pode não se aplicar diretamente aos seus requisitos específicos de backup.

### <a name="configure-storsimple"></a>Configure o StorSimple

| Tarefas de implantação do StorSimple                                                                                                                 | Comentários adicionais                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Implantação do seu dispositivo StorSimple local                                                                                                 | Versão com suporte: atualização 3 e posterior.                                                                                                                                                                                                                                                                 |
| Habilitar o modo de destino de backup                                                                                                                   | Use os comandos a seguir para habilitar/desabilitar e obter o status. Para obter mais informações, acesse [conectar -se remotamente a um StorSimple](storsimple-remote-connect.md).</br> Habilitar o modo de backup:`Set-HCSBackupApplianceMode -enable`</br>  Desabilitar o modo de backup:`Set-HCSBackupApplianceMode -disable`</br> Estado atual das configurações de modo de backup`Get-HCSBackupApplianceMode` |
| Crie um contêiner de volume comum para o seu volume que armazena os dados de backup.   Todos os dados de um contêiner de volume passam por eliminação de duplicação. | Contêineres de volume do StorSimple definem domínios de eliminação de duplicação.                                                                                                                                                                                                                                             |
| Criando volumes do StorSimple                                                                                                                 | Crie volumes com tamanhos o mais próximo possível do uso antecipado, já que o tamanho do volume afeta o tempo de duração do instantâneo de nuvem. Para obter mais informações sobre como dimensionar um volume, acesse [Políticas de retenção](#retention-policies).</br> </br> Use os volumes em camadas do StorSimple e selecione **Use este volume para dados de arquivamento acessados com menos frequência**. </br> Volumes afixados localmente apenas não têm suporte.|
| Crie uma política de backup do StorSimple exclusiva para todos os volumes de destino de backup.                                                               | Uma política de backup do StorSimple define o grupo de consistência do volume.                                                                                                                                                                                                                                       |
| Desabilitar a agenda como instantâneos.                                                                                                    | Os instantâneos são disparados como uma operação de pós-processamento.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |



### <a name="configure-host-backup-server-storage"></a>Configure o armazenamento do servidor de backup do host

Verifique se o armazenamento do servidor de backup do host está configurado de acordo com as diretrizes a seguir.  

- Não há suporte para volumes estendidos (criados pelo Gerenciador de Disco do Windows).
- Formate os volumes usando o NTFS com tamanho de unidade de alocação de 64 KB.
- Mapeie os volumes do StorSimple diretamente ao servidor do NetBackup. 
    - Use o iSCSI para servidores físicos.
    - Use discos de passagem para servidores virtuais.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Práticas recomendadas para StorSimple e NetBackup

Configure sua solução de acordo com as diretrizes a seguir.

### <a name="operating-system"></a>Sistema operacional

-   Desabilite a criptografia do Windows Server e a eliminação de duplicação do sistema de arquivos NTFS.

-   Desabilite a desfragmentação do Windows Server em volumes do StorSimple.

-   Desabilite a indexação do Windows Server em volumes do StorSimple.

-   Execute uma verificação antivírus no host de origem (não em relação aos volumes do StorSimple).

-   Desabilite o padrão [manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no gerenciador de tarefas.

    - Desabilitar o Configurador de manutenção no Agendador de Tarefas do Windows.

        ou o

    - Download: [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)

      - Depois de baixar o PSEXEC, execute o Windows PowerShell como administrador e digite:

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   Certifique-se de que o dispositivo StorSimple seja atualizado para a [Atualização 3 ou posterior](storsimple-install-update-3.md).

-   Isolar tráfego de iSCSI e de nuvem. Use conexões iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.

-   Verifique se o dispositivo StorSimple é um destino de backup dedicado. Não há suporte para cargas de trabalho mistas, já que essas cargas de trabalho afetam o RTO/RPO.

### <a name="netbackup"></a>NetBackup

-   O banco de dados do NetBackup deve ser local para o servidor e não residir em um volume do StorSimple.

-   Para recuperação de desastres, faça um backup do banco de dados do NetBackup em um volume StorSimple.

-   Oferecemos suporte a backups completos e incrementais do NetBackup para esta solução. É recomendável que você não use backups diferenciais e sintéticos.

-   Arquivos de dados de backup devem conter apenas dados de um trabalho específico. Por exemplo, não são permitidos acréscimos de mídia em vários trabalhos diferentes.

Para mais informações, confira [Documentação do NetBackup](https://www.veritas.com) para obter as definições mais recentes do NetBackup e as práticas recomendadas sobre como implementar esses requisitos.


## <a name="retention-policies"></a>Políticas de retenção

Uma das políticas de retenção mais usadas é a GFS (Grandfather, Father, and Son - avô, pai e filho). Nessa política, um backup incremental é executado diariamente. Os backups completos são feitos semanal e mensalmente. Essa política resulta em 6 volumes em camadas do StorSimple.

-   Um volume contém os backups completos semanais, mensais e anuais.

-   Os outros 5 volumes armazenam backups incrementais diários.

No exemplo a seguir, somos uma rotação de GFS. O exemplo pressupõe o seguinte:

-   Serão usados dados compactados ou com eliminação de duplicação.

-   Os backups completos têm 1 TiB cada.

-   Backups incrementais diários têm 500 GiB cada.

-   4 backups semanais mantidos por um mês.

-   12 backups mensais mantidos por um ano.

-   1 backup anual mantido por 10 anos.

Com base nas suposições anteriores, crie um volume em camadas StorSimple de 26 TiB para os backups completos mensais e anuais. Crie um volume em camadas StorSimple de 5 TiB para cada backup diário incremental.

| Retenção de tipo de backup | Tamanho TiB | Multiplicador GFS\*                                       | Capacidade total TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Completo semanal           | 1        | 4                                                      | 4                           |
| Incremental diário     | 0,5      | 20 (ciclos, igual ao número de semanas por mês) | 12 (2 para a cota adicional) |
| Mensal completo          | 1        | 12                                                     | 12                          |
| Anual completo           | 1        | 10                                                     | 10                          |
| Requisito de GFS       |          |                                                        | 38                          |
| Cota adicional      | 4        |                                                        | Requisito total de GFS 42   |

\*O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender às políticas de backup.

## <a name="configure-netbackup-storage"></a>Configurar o armazenamento do NetBackup


1.  No console de gerenciamento do NetBackup, selecione Dispositivos\> iniciar o Assistente de configuração do pool de disco\> selecione\> AdvancedDisk\>clique em Avançar

    ![Configuração do pool de disco do console de gerenciamento do NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

1.  Selecione seu servidor.

    ![Console de gerenciamento do NetBackup, selecione o servidor](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

1.  Em seguida, Avançar e selecione o volume do StorSimple

    ![Console de gerenciamento do NetBackup, selecione o disco do StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

1.  Em seguida, dê a ele um nome e clique em Avançar e Avançar para concluir

    ![Nome e tela de descrição do console de gerenciamento do NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage4.png)

    Depois de revisar as configurações, clique em Concluir.

    1.  No final de cada atribuição, altere as configurações do dispositivo de armazenamento de acordo com as configurações indicadas na lista de práticas recomendadas.

    2.  Repita as etapas 1 a 4 até concluir a atribuição de volumes do StorSimple.

    ![Configuração do disco do console de gerenciamento do NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de backup principal

> [!NOTE]
> Lembre-se de que se você precisar restaurar dados de um backup em camadas na nuvem, a restauração ocorrerá em velocidades de nuvem.

Na figura a seguir, ilustraremos o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco Sábado Completo e os backups incrementais são mapeados para discos incrementais Segunda a sexta. Todos os backups e restaurações são feitos a partir de um volume em camadas do StorSimple.

![Diagrama lógico de configuração de destino de backup principal ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Exemplo de agenda StorSimple como destino de backup principal GFS (Grandfather, Father and Son)

| Agendamento de rotação de GFS por quatro semanas, mensal e anual |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Tipo de frequência/ backup   | Completo          | Incremental (dia 1-5)  |
| Semanal (semanas 1 a 4)    | Sábado | Segunda a sexta |
| Mensal     | Sábado  |             |
| Anual      | Sábado  |             |


### <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Atribuir volumes do StorSimple a um trabalho de backup do NetBackup

A sequência a seguir pressupõe que NetBackup e o host de destino estão configurados de acordo com as diretrizes de agente do NetBackup.

1.  No console de gerenciamento do NetBackup, selecione uma Política\> clique com o botão direito em\>Nova política

    ![Tela do console de gerenciamento do NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

1.  Selecione um nome de política e use o assistente da política

    ![Console de gerenciamento do NetBackup, nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

1.  Selecione o tipo de backup apropriado, no nosso caso, Sistemas de arquivo, e depois Avançar

    ![Console de gerenciamento do NetBackup, nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

1.  Em seguida, selecione padrão

    ![Console de gerenciamento do NetBackup, nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

1.  Selecione o host, selecione Detectar sistema operacional do cliente e Adicionar

    ![Console de gerenciamento do NetBackup, nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

1.  Em seguida, Avançar

2.  Em seguida, selecione as unidades das quais você precisa fazer backup, no nosso caso, G\\DataChange3\\

    ![Console de gerenciamento do NetBackup, nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

1.  Selecione a rotação que atenda às suas necessidades

    ![Console de gerenciamento do NetBackup, nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

1.  Em seguida, Avançar e Avançar e Concluir. Vamos modificar o agendamento depois que a política for criada.

2.  Expanda a política que você criou e selecione agendamentos.

    ![Console de gerenciamento do NetBackup, nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

1.  Clique com o botão direito em diferencial-inc e selecione copiar para novo, no nosso caso, Segunda-inc, e clique em OK

    ![Console de gerenciamento do NetBackup, agendamento da nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

1.  Depois, clique com o botão direito do mouse no agendamento recém-criado e selecione alterar.

2.  Na guia Atributos, marque Substituir seleção de armazenamento da política e selecione o volume para onde vão os backups incrementais de segunda-feira. No nosso caso, SS1

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

1.  Na janela Iniciar, selecione a janela para seus backups. No nosso caso, segundas-feiras das 8:00 às 21:00

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

1.  Selecione OK

2.  Repita as etapas 11 a 15 para cada um dos backups incrementais e selecione o volume e o agendamento apropriados.

3.  Em seguida, clique com o botão direito do mouse na agenda Diferencial-inc e a exclua.

4.  Depois de excluir a agenda Diferencial-inc, modifique a agenda completa para atender às suas necessidades.

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

1.  Altere a janela de inicialização, no nosso caso, sábado às 06:00

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

1.  O agendamento final deve ficar assim

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de backup secundário

> [!NOTE]
> Lembre-se de que se você precisar restaurar dados de um backup em camadas na nuvem, a restauração ocorrerá em velocidades de nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (que não seja StorSimple) para servir como um cache temporário. Por exemplo, você poderia usar um volume RAID para acomodar a largura de banda, IOs e espaço. É recomendável usar RAID 5, 50 e 10.

Na figura a seguir, mostraremos os volumes locais (para o servidor) de retenção de curto prazo e os volumes de arquivamento para retenção de longo prazo. Nesse caso, todos os backups são executados no volume RAID local (para o servidor). Esses backups são duplicados e arquivados periodicamente em um volume de arquivamento. É importante dimensionar seu volume RAID local (para o servidor) para lidar com os requisitos de capacidade e desempenho da retenção de curto prazo.

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS de destino de backup secundário

![StorSimple como um diagrama lógico de destino de backup secundário ](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

A tabela a seguir ilustra como os backups devem ser configurados para execução no local e nos discos do StorSimple, incluindo os requisitos de capacidade total e individual.

#### <a name="backup-configuration-and-capacity-requirements"></a>Configuração de backup e requisitos de capacidade

| Tipo de backup e retenção                    |Armazenamento configurado| Tamanho (TiB) | Multiplicador GFS | Capacidade total (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Semana 1 (completa e incremental) |Disco local (curto prazo)| 1        | 1              | 1           |
| StorSimple semanas 2 a 4           |Disco do StorSimple (longo prazo) | 1        | 4              | 4                   |
| Mensal completo                                 |Disco do StorSimple (longo prazo) | 1        | 12             | 12                   |
| Anual completo                               |Disco do StorSimple (longo prazo) | 1        | 1              | 1                   |
|Requisito de tamanho de volumes do GFS | |          |                | 18*|

\* a capacidade total inclui 17 TiB de discos do StorSimple e 1 TiB de volume RAID local


#### <a name="gfs-example-schedule"></a>Exemplo de agenda do GFS

|Agenda semanal, mensal e anual da rotação do GFS|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Semana                                                                     | Completo               | Incremental dia 1        | Incremental dia 2        | Incremental dia 3        | Incremental dia 4        | Incremental dia 5        |
| Semana 1                                                                   | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana 2                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Semana 3                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Semana 4                                                                   | StorSimple semanas 2 a 4 |                   |                   |                   |                   |                   |
| Mensal                                                                  | StorSimple mensal |                   |                   |                   |                   |                   |
| Anual                                                                   | StorSimple anual  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-netbackup-archiveduplication-job"></a>Atribua volumes do StorSimple ao trabalho de arquivamento/duplicação do NetBackup.

Considerando a ampla gama de opções de armazenamento e gerenciamento de mídia que o NetBackup oferece, consulte o Veritas ou seu Arquiteto do NetBackup para avaliar os requisitos do SLP adequadamente.

1.  Depois que os pools de disco iniciais tiverem sido definidos, prossiga para definir 3 políticas de ciclo de vida de armazenamento:

    1.  LocalRAIDVolume

    2.  StorSimpleWeek2-4

    3.  StorSimpleMonthlyFulls

    4.  StorSimpleYearlyFulls

    No console de gerenciamento, em Armazenamento, selecione Políticas de ciclo de vida de armazenamento e selecione Nova política de ciclo de vida de armazenamento

    [Console de gerenciamento do NetBackup, política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

1.  Selecione um nome e clique em Adicionar

    ![Console de gerenciamento do NetBackup, política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage21.png)

1.  Na guia Propriedades, selecione Backup, Armazenamento de destino, a retenção apropriada e OK. No nosso caso, LocalRAIDVolume e retenção de uma semana.

    ![Console de gerenciamento do NetBackup, política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

1.  Isso definiu a primeira operação/repositório de backup

2.  Enquanto destaca a operação anterior, clique em Adicionar, selecione o Armazenamento de destino e a retenção apropriada. No nosso caso, de LocalRAIDVolume a StorSimpleWeek2-4 e retenção de 1 mês

    ![Console de gerenciamento do NetBackup, nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

1.  Enquanto destaca a operação anterior, selecione Adicionar, agora adicionando os backups mensais por um ano

    ![Console de gerenciamento do NetBackup, alterar política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

1.  Repita as etapas 5 a 6 até obter a política de retenção SLP apropriada.

    ![Console de gerenciamento do NetBackup, política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

1.  Depois de definir a política de retenção de SLP apropriada, em Política defina uma política de backup conforme ilustrado no StorSimple como seção de destino principal.

1.  Em Agendas, selecione Completo e clique com o botão direito do mouse, depois selecione Alterar.

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

1.  Depois selecione Substituir seleção de armazenamento de política e selecione o SLP criado nas etapas 1-8

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

1.  Clique em OK e repita o processo para o agendamento de backup incremental.

    ![Console de gerenciamento do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)

1.  Clique em OK e repita o processo para o agendamento de backup incremental.

| Retenção de tipo de backup | Tamanho TiB | Multiplicador GFS\*                                       | Capacidade total TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Completo semanal           | 1        | 4                                                      | 4                           |
| Incremental diário     | 0,5      | 20 “ciclos são iguais ao número de semanas por mês” | 12 (2 para a cota adicional) |
| Mensal completo          | 1        | 12                                                     | 12                          |
| Anual completo           | 1        | 10                                                     | 10                          |
| Requisito de GFS       |          |                                                        | 38                          |
| Cota adicional      | 4        |                                                        | Requisito total de GFS 42.   |
\*O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender às políticas de backup.

### <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem StorSimple protegem os dados que residem no dispositivo StorSimple. Isso equivale a enviar fitas para uma instalação externa e, em caso de uso do GRS (armazenamento com redundância geográfica do Azure), a enviar fitas para vários sites. Se uma restauração de dispositivo fosse necessária em caso de desastre, você poderia colocar outro dispositivo StorSimple on-line e executar um failover. Após o failover, você poderia acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.

A seção a seguir ilustra como criar um script curto para acionar e excluir instantâneos de nuvem StorSimple durante o pós-processamento de backup.

> [!NOTE]
> Os instantâneos criados manualmente ou por meio de programação não seguem a política de expiração de instantâneos do StorSimple. Eles devem ser excluídos manual ou programaticamente.

### <a name="start-delete-cloud-snapshots-with-a-script"></a>Iniciar-Excluir instantâneos de nuvem com um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e de retenção de dados antes de excluir um instantâneo do StorSimple. Para saber mais sobre como executar um script pós-backup, consulte a [documentação do NetBackup](https://www.veritas.com/support/article.000094423).

#### <a name="backup-lifecycle"></a>Ciclo de vida de backup

![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

#### <a name="requirements"></a>Requisitos:

-   O servidor que executa o script deve ter acesso à nuvem do Azure.

-   A conta de usuário deve ter as permissões necessárias.

-   Uma política de backup do StorSimple com os volumes StorSimple associados configurada, mas não habilitada.

-   Nome do recurso de StorSimple, chave de registro, nome do dispositivo e ID da política de backup.

#### <a name="steps"></a>Etapas:

1.  [Instale o Azure PowerShell](/powershell-install-configure/).

2.  [Baixe e importe informações de assinatura e configurações de publicação.](https://msdn.microsoft.com/library/dn385850.aspx)

3.  No portal clássico do Azure, obtenha o nome do recurso e a [chave de registro para o serviço StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

4.  No servidor que executa o script, execute o Windows PowerShell como administrador. Tipo:

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Anote a ID da política de backup.

5.  No bloco de notas, crie um novo Script do Windows PowerShell e salve-o no mesmo local em que você salvou as configurações de publicação do Azure. Por exemplo: `C:\CloudSnapshot\StorSimpleCloudSnapshot.ps1`.

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

6.  Adicione o script ao seu trabalho de backup no NetBackup editando seus comandos de pré-publicação das opções do trabalho do NetBackup

> [!NOTE]
> Recomendamos que você execute a política de backup de instantâneo de nuvem StorSimple ao concluir o trabalho de backup diário como um script de pós-processo. Para saber mais sobre como fazer backup e restaurar o ambiente de aplicativo de backup para atender ao seu RPO/RTO, consulte o arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como origem de restauração


As restaurações de um StorSimple funcionam mais ou menos como qualquer dispositivo de armazenamento em bloco. Ao restaurar os dados que estão em camadas na nuvem, a restauração ocorre em velocidades de nuvem. Para dados locais, a restauração ocorre na velocidade do disco local do dispositivo. Para saber mais sobre como executar uma restauração, consulte a [documentação do NetBackup](https://www.veritas.com/support/article.000094423) e as práticas recomendadas do NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o StorSimple Cloud Appliance não tem suporte como um destino de restauração.

Um desastre pode ocorrer devido a vários fatores. A tabela a seguir lista cenários comuns de recuperação de desastre (DR).

| Cenário                                                                    | Impacto                                             | Como recuperar                                                                                                                                                                               | Observações                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Falha de um dispositivo StorSimple                                                | As operações de backup e restauração serão interrompidas. | Substitua o dispositivo com falha e execute o [Failver do StorSimple e recuperação de desastres](storsimple-device-failover-disaster-recovery.md) | Se houver a necessidade de executar uma restauração após a recuperação do dispositivo, os conjuntos de trabalho de dados completos serão recuperados da nuvem para o novo dispositivo, o que fará com que todas as operações passem a ocorrer na velocidade da nuvem. Esse processo de nova verificação da indexação e catalogação pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local, o que pode ser um processo demorado.                                                 |
| Falha do servidor NetBackup                                              | As operações de backup e restauração foram interrompidas. | Recriar o servidor de backup e executar a restauração do banco de dados                                                                                                                                       | O servidor NetBackup precisará ser recriado ou restaurado no local de recuperação de desastres. O banco de dados precisa ser restaurado para o ponto mais recente. Se o banco de dados restaurado do NetBackup não estiver sincronizado com os trabalhos de backup mais recentes, será necessário fazer a indexação e a catalogação. Esse processo de nova verificação do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam verificados e extraídos da camada da nuvem para a camada do dispositivo local. Isso faz com que o tempo faça ainda mais diferença. |
| Falha do site que resulta na perda do servidor de backup e do StorSimple. | As operações de backup e restauração foram interrompidas. | Restaure o StorSimple primeiro e, depois, o NetBackup.                                                                                                                                          | Restaure o StorSimple primeiro e, depois, o NetBackup.                                                                 Se houver a necessidade de realizar uma restauração após a recuperação do dispositivo, os conjuntos de dados de trabalho totais são recuperados da nuvem para o novo dispositivo. Em função disso, todas as operações ocorrem em velocidades de nuvem.|

## <a name="references"></a>Referências

Os documentos a seguir foram mencionados neste artigo:

- [Instalação de MPIO do StorSimple](storsimple-configure-mpio-windows-server.md)

- [Cenários de armazenamento: provisionamento dinâmico](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Usando unidades GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Habilitar e configurar cópias de sombra de pastas compartilhadas](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Próximas etapas

Saiba mais:

- Sobre como [Restaurar a partir de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Sobre como executar [Recuperação de desastre e failover de dispositivo](storsimple-device-failover-disaster-recovery.md).



<!--HONumber=Dec16_HO2-->


