---
title: Backup e restauração do HANA no SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como realizar o backup e a restauração do HANA no SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987906"
---
# <a name="backup-and-restore"></a>Backup e restauração

>[!IMPORTANT]
>Este artigo não é uma substituição para a documentação de administração do SAP HANA ou notas SAP. Esperamos que você tenha uma compreensão sólida dos e experiência em administração do SAP HANA e operações, especialmente para backup, restauração, alta disponibilidade e recuperação de desastres. Neste artigo, são mostradas capturas de tela do SAP HANA Studio. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

É importante que você execute as etapas e os processos realizados em seu ambiente e com as liberações e versões do HANA. Alguns processos descritos neste artigo são simplificados para um melhor entendimento geral. Eles não deveriam ser usados como etapas detalhadas para eventuais manuais de operação. Se você quiser criar manuais de operação para suas configurações, testar e executar os processos e documentar os processos relacionados às suas configurações específicas. 

Um dos aspectos mais importantes de bancos de dados operacionais é protegê-los contra eventos catastróficos. A causa desses eventos pode ser qualquer coisa, desde desastres naturais a simples erros de usuário.

Fazendo backup de um banco de dados, com a capacidade de restaurar para qualquer ponto no tempo, como antes de alguém excluídos dados críticos, permite a restauração para um estado que é o mais próximo possível à forma como ele estava antes da interrupção.

Dois tipos de backup devem ser executados para atingir a capacidade de restaurar:

- Backups de banco de dados: backups completos, incrementais ou diferenciais
- Backups do log de transações

Além de backups completos do banco de dados realizados em um nível de aplicativo, é possível realizar backups com instantâneos de armazenamento. Instantâneos de armazenamento não substituem backups de log de transações. Os backups de log de transações continuam sendo importantes para restaurar o banco de dados para um determinado ponto no tempo ou para esvaziar os logs de transações já confirmadas. Instantâneos de armazenamento podem acelerar a recuperação fornecendo rapidamente uma imagem de roll forward do banco de dados. 

O SAP HANA no Azure (Instâncias Grandes) oferece duas opções de backup e de restauração:

- **Fazê-lo DIY ().** Depois que você certifique-se de que há espaço em disco suficiente, execute o banco de dados completo e backups de log usando um dos seguintes métodos de backup de disco. Você pode fazer backup diretamente para volumes anexados às unidades de instância grande do HANA ou para compartilhamentos NFS que são definidos em uma máquina virtual do Azure (VM). No último caso, os clientes configuram uma VM do Linux no Azure, anexam o Armazenamento do Azure à VM e compartilham o armazenamento por meio de um servidor NFS configurado na VM. Se você executar o backup em volumes anexados diretamente às unidades de instância grande do HANA, copie os backups para uma conta de armazenamento do Azure. Fazer isso depois de configurar uma VM do Azure que exporta compartilhamentos NFS que se baseiam no armazenamento do Azure. Você também pode usar um cofre de Backup do Azure ou armazenamento frio do Azure. 

   Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar os backups depois que eles são copiados para uma conta de armazenamento do Azure. A opção de backup DIY também pode ser necessária para dados que você precisa armazenar por longos períodos de tempo para fins de auditoria e conformidade. Em todos os casos, os backups são copiados para compartilhamentos NFS representados por meio de uma VM e do Armazenamento do Azure.

- **Infraestrutura de backup e restaurar a funcionalidade.** Você também pode usar o backup e restaurar a funcionalidade que fornece a infraestrutura subjacente do SAP HANA no Azure (instâncias grandes). Essa opção atende a necessidade de backups e restaurações rápidas. O restante desta seção aborda a funcionalidade de backup e de restauração oferecida com Instâncias Grandes HANA. Esta seção também aborda a relação que têm de backup e restauração para o desastre funcionalidade de recuperação oferecida pelas instâncias grandes HANA.

> [!NOTE]
>   A tecnologia de instantâneo que é usada pela infraestrutura subjacente das instâncias grandes HANA tem uma dependência de instantâneos do SAP HANA. Neste ponto, os instantâneos do SAP HANA não funcionam em conjunto com múltiplos locatários dos contêineres de banco de dados multilocatário do SAP HANA. Se apenas um locatário for implantado, os instantâneos do SAP HANA funcionam e você pode usar esse método.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usar instantâneos de armazenamento do SAP HANA no Azure (instâncias grandes)

A infraestrutura de armazenamento subjacente ao SAP HANA no Azure (Instâncias Grandes) dá suporte a instantâneos de armazenamento de volumes. Há suporte para o backup e para a restauração de volumes, com as seguintes considerações:

- Em vez de backups de banco de dados completos, instantâneos de volume de armazenamento são executados com frequência.
- Quando um instantâneo é disparado em /hana/data e /hana/shared, que inclui /usr/sap, volumes, a tecnologia de instantâneo inicia um instantâneo antes do SAP HANA é executado o instantâneo de armazenamento. Esse instantâneo SAP HANA é o ponto de instalação para restaurações de log eventual após a recuperação do instantâneo de armazenamento. Para um instantâneo do HANA seja bem-sucedida, você precisa de uma instância ativa do HANA. Em um cenário com HSR, um instantâneo de armazenamento não tem suporte em um nó secundário atual em que um instantâneo do HANA não pode ser executado.
- Depois que o instantâneo de armazenamento é executado com êxito, o instantâneo SAP HANA é excluído.
- Backups de log de transações são usados frequentemente e armazenados no volume /hana/logbackups ou no Azure. É possível disparar o volume /hana/logbackups que contém os backups de log de transações para usar um instantâneo separadamente. Nesse caso, você não precisa executar um instantâneo do HANA.
- Se você precisar restaurar um banco de dados para um determinado ponto no tempo, para uma interrupção de produção, solicitar esse suporte do Microsoft Azure ou do SAP HANA na restauração do Azure para um determinado instantâneo de armazenamento. Um exemplo é uma restauração planejada de um sistema de área restrita para seu estado original.
- O instantâneo SAP HANA que está incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log de transações que foram executados e foram armazenados após o instantâneo de armazenamento foi tirado.
- Esses backups de log de transações são aplicados para restaurar o banco de dados para um determinado ponto no tempo.

É possível executar instantâneos de armazenamento que se destinam a três classes de volumes:

- Um instantâneo combinado em hana/data e hana/shared, que inclui /usr/sap. Esse instantâneo exige a criação de um instantâneo do SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo SAP HANA garante que o banco de dados está em um estado consistente do ponto de vista de armazenamento. Para o processo de restauração, que é um ponto para configurar no.
- Um instantâneo separado em /hana/logbackups.
- Uma partição do sistema operacional.

Para obter os scripts de instantâneo e a documentação mais recentes, consulte [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Quando você baixa o pacote de script de instantâneo no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), você obtém três arquivos. Um dos arquivos está documentado em um PDF para a funcionalidade fornecida. Depois de baixar o conjunto de ferramentas, siga as instruções em "obter as ferramentas de instantâneo".

## <a name="storage-snapshot-considerations"></a>Considerações sobre o instantâneo de armazenamento

>[!NOTE]
>Instantâneos de armazenamento consomem espaço de armazenamento é alocado para as unidades de instância grande do HANA. Considere os seguintes aspectos da programação de instantâneos de armazenamento e quantos instantâneos de armazenamento para manter. 

A mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (Instâncias Grandes) inclui:

- Um instantâneo de armazenamento específica no ponto no tempo que é obtido consome pouco de armazenamento.
- Como as alterações de conteúdo de dados e o conteúdo na alteração dos arquivos no volume de armazenamento de dados de SAP HANA, o instantâneo precisa armazenar o conteúdo do bloco original e as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existe, quanto maior o instantâneo de armazenamento ficar.
- Quanto mais alterações são feitas no volume de banco de dados do SAP HANA durante o tempo de vida de um instantâneo de armazenamento, maior o consumo de espaço do instantâneo de armazenamento.

O SAP HANA no Azure (Instâncias Grandes) vem com tamanhos de volume fixo para o volume de dados e de log do SAP HANA. A execução de instantâneos desses volumes consome seu espaço de volume. Você precisa:

- Determine quando agendar instantâneos de armazenamento.
- Monitore o consumo de espaço dos volumes de armazenamento. 
- Gerencie o número de instantâneos que você armazena. 

É possível desabilitar os instantâneos de armazenamento quando você importa muitos dados ou realiza outras alterações significativas no banco de dados do HANA. 


As seções a seguir fornecem informações para executar esses instantâneos e incluem recomendações gerais:

- Embora o hardware pode sustentar 255 snapshots por volume, você deseja fique bem abaixo desse limite. A recomendação é 250 ou menos.
- Antes de executar instantâneos de armazenamento, monitore e mantenha o controle do espaço livre.
- Reduza o número de instantâneos de armazenamento com base em espaço livre. É possível reduzir o número de instantâneos que você mantém ou aumentar os volumes. É possível solicitar armazenamento adicional em unidades de 1 terabyte.
- Durante atividades como a movimentação de dados em SAP HANA com ferramentas de migração da plataforma SAP (R3load) ou a restauração de bancos de dados do SAP HANA com base em backups, desabilite instantâneos de armazenamento no volume /hana/data. 
- Durante maiores reorganizações de tabelas do SAP HANA, evite instantâneos de armazenamento se possível.
- Os instantâneos de armazenamento são um pré-requisito para usar os recursos de recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para usar os instantâneos de armazenamento de autoatendimento

Para certificar-se de que o script de instantâneo é executado com êxito, certifique-se de que Perl está instalado no sistema operacional Linux no servidor do HANA em instâncias grandes. Perl vem pré-instalado em sua unidade de instância grande do HANA. Para verificar a versão do Perl, use a linha de comando a seguir:

`perl -v`

![A chave pública é copiada executando este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar instantâneos de armazenamento com instâncias grandes do HANA, siga estas etapas.
1. Verifique se o Perl está instalado no sistema operacional Linux no servidor das Instâncias Grandes HANA.
1. Modifique /etc/ssh/ssh\_config para adicionar a linha _MACs hmac-sha1_.
1. Crie uma conta de usuário de backup do SAP HANA no nó mestre para cada instância do SAP HANA que você executar, se aplicável.
1. Instale o cliente SAP HANA HDB em todos os servidores de Instâncias Grandes do SAP HANA.
1. No primeiro servidor de Instâncias Grandes do SAP HANA de cada região, crie uma chave pública para acessar a infraestrutura de armazenamento subjacente que controla a criação de instantâneos.
1. Copie os scripts e o arquivo de configuração do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) para o local do **hdbsql** na instalação do SAP HANA.
1. Modifique o arquivo *HANABackupDetails.txt* conforme necessário para as especificações apropriadas do cliente.

Obtenha os scripts de instantâneo e a documentação mais recentes do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Para obter as etapas listadas anteriormente, consulte [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Consideração para cenários de MCOD
Se você executar uma [cenário de MCOD](https://launchpad.support.sap.com/#/notes/1681092) com várias instâncias do SAP HANA em uma unidade de instância grande do HANA, você tem volumes de armazenamento separados provisionados para cada uma das instâncias do SAP HANA. Para obter mais informações sobre MDC e outras considerações, consulte "Coisas importantes para lembrar" em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Etapa 1: Instalar o cliente do SAP HANA HDB

O sistema de operacional Linux instalado em SAP HANA no Azure (instâncias grandes) inclui as pastas e os scripts necessários para executar instantâneos de armazenamento do SAP HANA para o desastre e backup fins de recuperação. Procure versões mais recentes em [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A versão mais recente dos scripts é 4.0. Scripts diferentes podem ter diferentes versões secundárias dentro da mesma versão principal.

É sua responsabilidade instalar o cliente SAP HANA HDB nas unidades de instância grande do HANA enquanto você instala o SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Etapa 2: Alterar a /etc/ssh/ssh\_config

Essa etapa é descrita em "Habilitar a comunicação com o armazenamento" em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>Etapa 3: Criar uma chave pública

Para habilitar o acesso às interfaces de instantâneo de armazenamento do seu locatário de instância grande do HANA, estabeleça um procedimento de entrada por meio de uma chave pública. 

Sobre o primeiro servidor SAP HANA no Azure (instâncias grandes) em seu locatário, crie uma chave pública para acessar a infraestrutura de armazenamento. Com uma chave pública, não é necessária uma senha para entrar em interfaces de instantâneo de armazenamento. Você também não precisa manter credenciais de senha com uma chave pública. 

Para gerar uma chave pública, consulte "Habilitar a comunicação com o armazenamento" em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Etapa 4: Criar uma conta de usuário do SAP HANA

Para iniciar a criação de instantâneos do SAP HANA, crie uma conta de usuário no SAP HANA que os scripts de instantâneo de armazenamento podem usar. Crie uma conta de usuário do SAP HANA no SAP HANA Studio para essa finalidade. O usuário deve ser criado no SystemDB, e *não* sob o banco de dados do SID para MDC. No ambiente de contêiner único, o usuário é criado no banco de dados de locatário. Essa conta deve ter **administrador de Backup** e **catálogo leitura** privilégios. 

Para configurar e usar uma conta de usuário, consulte "Habilitar a comunicação com o SAP HANA" na [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Etapa 5: Autorizar a conta de usuário do SAP HANA

Nesta etapa, você deve autorizar a conta de usuário do SAP HANA que você criou para que os scripts não precisem enviar senhas em tempo de execução. O comando do SAP HANA `hdbuserstore` permite a criação de uma chave de usuário do SAP HANA. A chave é armazenada em um ou mais nós SAP HANA. A chave de usuário permite que ele acesse o SAP HANA sem precisar gerenciar senhas de dentro do processo de script. O processo de script será abordado posteriormente neste artigo.

>[!IMPORTANT]
>Execute estes comandos de configuração com o mesmo contexto de usuário que os comandos de instantâneo são executados no. Caso contrário, os comandos de instantâneo não funcionará corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Etapa 6: Obter os scripts de instantâneo, configurar os instantâneos e testar a configuração e a conectividade

Baixe a versão mais recente dos scripts no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A maneira como os scripts são instalados mudou com a versão 4.0 dos scripts. Para obter mais informações, consulte "Habilitar a comunicação com o SAP HANA" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Para a sequência exata dos comandos, consulte "Facilitar a instalação das ferramentas de instantâneo (padrão)" em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Recomendamos o uso da instalação padrão. 

Para atualizar da versão 3.x para 4.0, consulte "Atualizar uma instalação existente" em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Para desinstalar o conjunto de 4.0 ferramentas, consulte "Desinstalação das ferramentas do instantâneo" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Não se esqueça de executar as etapas descritas em "Concluir a instalação das ferramentas de instantâneo" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A finalidade dos diferentes scripts e arquivos que obteve instaladas é descrita em "Quais são essas ferramentas de instantâneo?" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Antes de configurar as ferramentas de instantâneo, certifique-se de também configurado configurações e os locais de backup do HANA corretamente. Para obter mais informações, consulte "Configuração do SAP HANA" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A configuração do conjunto de ferramentas de instantâneo é descrita em "Arquivo Config - Hanabackupcustomerdetails" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Testar a conectividade com o SAP HANA

Após colocar todos os dados de configuração no arquivo *HANABackupCustomerDetails.txt* verifique se as configurações estão corretas para os dados da instância do HANA. Use o script `testHANAConnection`, que é independente de uma configuração de expansão ou escala vertical do SAP HANA.

Para obter mais informações, consulte "Verifique a conectividade com o SAP HANA - testHANAConnection" em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Testar a conectividade de armazenamento

A próxima etapa do teste é verificar a conectividade com o armazenamento com base nos dados que você colocar nas *Hanabackupcustomerdetails* arquivo de configuração. Em seguida, execute um instantâneo de teste. Antes de executar o `azure_hana_backup` de comando, você deve executar esse teste. Para a sequência de comandos para este teste, consulte "Verifique a conectividade com o armazenamento - testStorageSnapshotConnection" "em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Após uma conexão com êxito nas interfaces da máquina virtual de armazenamento, o script continuará com a fase 2 e criará um instantâneo de teste. A saída é mostrada aqui para uma configuração de expansão de três nós do SAP HANA.

Se o instantâneo de teste for executado com êxito com o script, você pode agendar instantâneos de armazenamento real. Se não for bem-sucedida, investigue os problemas antes de Avançar. O instantâneo de teste deve permanecer até que os primeiros instantâneos reais sejam concluídos.


### <a name="step-7-perform-snapshots"></a>Etapa 7: Executar instantâneos

Quando as etapas de preparação estiverem concluídas, você pode começar a configurar e agendar instantâneos de armazenamento real. O script a ser agendado funciona com as configurações de expansão e escalável do SAP HANA. Para execução periódica e regular do script de backup, agende o script usando o utilitário cron. 

Para a sintaxe de comando exata e funcionalidade, consulte o "Backup de instantâneo executar - azure_hana_backup" [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Quando o script `azure_hana_backup` é executado, ele cria o instantâneo de armazenamento a seguir três fases:

1. Ele executa um instantâneo SAP HANA.
1. Ele executa um instantâneo de armazenamento.
1. Ele remove o instantâneo SAP HANA que foi criado antes de executar o instantâneo de armazenamento.

Para executar o script, chamá-lo na pasta executável HDB para o qual ele foi copiado. 

O período de retenção é administrado com o número de instantâneos enviados como um parâmetro ao executar o script. A quantidade de tempo coberta pelos instantâneos de armazenamento é uma função do período de execução e do número de instantâneos enviado como um parâmetro quando o script é executado. 

Se o número de instantâneos que é mantido exceder o número identificado como um parâmetro na chamada do script, o instantâneo de armazenamento mais antigo do mesmo rótulo será excluído antes que um novo instantâneo é executado. O número que você der como o último parâmetro da chamada é o número que você poderá usar para controlar o número de instantâneos mantidos. Com esse número, você também pode controlar, indiretamente, o espaço em disco que é usado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência dos instantâneos para os diferentes tipos depende do fato de você usar a funcionalidade de recuperação de desastre do SAP HANA em Instâncias Grandes. Essa funcionalidade depende de instantâneos de armazenamento, os quais podem requerer recomendações especiais para os períodos de execução e frequência dos instantâneos de armazenamento. 

Nas considerações e recomendações a seguir, vamos supor que você *não* usa a funcionalidade de recuperação de desastre que o SAP HANA em Instâncias Grandes oferece. Em vez disso, você usa os instantâneos de armazenamento para ter backups e ser capaz de fornecer recuperação pontual nos últimos 30 dias. Devido às limitações do número de instantâneos e espaço, considere os seguintes requisitos:

- O tempo de recuperação para uma restauração pontual.
- O espaço usado.
- O ponto de recuperação e os objetivos de tempo de recuperação para possível recuperação de um desastre.
- A execução eventual de backups completos de banco de dados do HANA em discos. Sempre que um backup completo do banco de dados em discos ou a interface **backint** for realizado, a execução de instantâneos de armazenamento falhará. Se você planeja executar backups de banco de dados completos sobre instantâneos de armazenamento, certifique-se de que a execução dos instantâneos de armazenamento é desabilitada durante esse tempo.
- O número de instantâneos por volume, que é limitada a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se você não usar a funcionalidade de recuperação de desastre de instâncias grandes HANA, o período de instantâneo é menos frequente. Nesses casos, executam os instantâneos combinados em /hana/data e /hana/shared, que inclui /usr/sap, em períodos de 12 horas ou 24 horas. Mantenha os instantâneos por um mês. O mesmo é verdadeiro para os instantâneos do volume de backup de log. A execução de backups de log de transações do SAP HANA no volume de backup de log ocorre em 5 minutos a períodos de 15 minutos.

Os instantâneos de armazenamento agendados executam melhor utilizando o cron. Use o mesmo script para todos os backups e necessidades de recuperação de desastres. Modifique as entradas de script para corresponder aos vários tempos de backup solicitados. Esses instantâneos são todos agendados de forma diferente no cron dependendo de seu tempo de execução. Ele pode ser por hora, cada 12 horas, diária, ou semanalmente. 

O exemplo a seguir mostra um agendamento de cron em /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, um instantâneo combinado por hora aborda os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/sap locais. Use esse tipo de instantâneo para uma recuperação pontual mais rápida nos últimos dois dias. Também é um instantâneo nos volumes de diário. Portanto, você tem dois dias de cobertura por instantâneos por hora além de quatro semanas de cobertura por instantâneos diários. O volume de backup de log de transação também é feito backup diário. Esses backups são mantidos por quatro semanas. 

Como você ver na terceira linha de crontab, o backup do log de transações HANA está agendado para ser executado a cada 5 minutos. As horas de início dos trabalhos de cron diferentes que executam os instantâneos de armazenamento são alternadas. Dessa forma, os instantâneos não são executados ao mesmo tempo em um determinado ponto no tempo. 

No exemplo a seguir, você executa um instantâneo combinado que cobre os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/sap, locais por hora. Mantenha esses instantâneos por dois dias. Os instantâneos de volumes de log de transações backup executado em uma base de 5 minutos e são mantidos por quatro horas. Como antes, o backup do arquivo de log de transações HANA está agendado para ser executado a cada 5 minutos. 

O instantâneo do volume de backup de log de transações é executado com um atraso de 2 minutos após o backup de log de transações ter sido inciado. Em circunstâncias normais, o backup de log de transações do SAP HANA termina dentro desses 2 minutos. Como antes, o volume que contém o LUN de inicialização tem seu backup feito uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico a seguir ilustra as sequências do exemplo anterior. O LUN de inicialização é excluída.

![Relação entre os backups e os instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

O SAP HANA executa gravações regulares no volume /hana/log para documentar as alterações confirmadas no banco de dados. Regularmente, o SAP HANA grava um ponto de salvamento para o volume /hana/data. Conforme especificado em crontab, um backup de log de transações do SAP HANA é executado a cada 5 minutos. 

Você também pode ver que um instantâneo SAP HANA é executado a cada hora, como resultado do acionamento de um instantâneo nos volumes /hana/data e /hana/shared/SID de armazenamento combinado. Depois que o instantâneo HANA for bem-sucedida, o armazenamento combinado de instantâneo é executado. Conforme instruído em crontab, o instantâneo de armazenamento no volume /hana/logbackup é executado a cada 5 minutos, aproximadamente 2 minutos após o backup de log de transações do HANA.

> 

>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups do SAP HANA só é válido quando os instantâneos são executados junto com backups de log de transações do SAP HANA. Esses backups de log de transações precisam abordar os períodos de tempo entre os instantâneos de armazenamento. 

Se você firmou um compromisso com os usuários de uma recuperação pontual de 30 dias, será necessário:

- Acesse um instantâneo de armazenamento combinado no /hana/data e /hana/shared/SID que tem 30 dias, em casos extremos. 
- Tenha backups de log de transações contíguos que abrangem o tempo entre todos os instantâneos de armazenamento combinado. Portanto, o instantâneo mais antigo do volume de backup de log de transações deve ser de 30 dias. Isso não é o caso se você copiar os backups de log de transações para outro compartilhamento NFS localizado no armazenamento do Azure. Nesse caso, você pode efetuar pull de backups de log de transações antigos desse compartilhamento NFS.

Para se beneficiar de instantâneos de armazenamento e a replicação de armazenamento eventual dos backups de log de transações, altere o local no qual o SAP HANA grava os backups de log de transações. É possível fazer essa alteração no HANA Studio. 

Embora o SAP HANA faz backup dos segmentos de log completos automaticamente, especifique um intervalo de backup de log para ser determinístico. Isso é especialmente verdadeiro quando você usa a opção de recuperação de desastre, porque você geralmente deseja executar backups de log com um período determinístico. Caso a seguir, 15 minutos é definido como o intervalo de backup de log.

![Agendar logs de backup SAP HANA no Studio de SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Você também pode escolher os backups que são mais frequentes do que a cada 15 minutos. Uma configuração mais frequente é frequentemente usada em conjunto com a funcionalidade de recuperação de desastre do SAP HANA nas Instâncias Grandes. Alguns clientes executam backups de log de transações a cada 5 minutos.

Se o backup do banco de dados nunca foi feito, a etapa final será executar um backup de banco de dados com base no arquivo para criar uma entrada de backup única que deve existir no catálogo de backup. Caso contrário, SAP HANA não pode iniciar os backups de log especificado.

![Faça um backup baseado em arquivo para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Depois de executar seus primeiros instantâneos de armazenamento bem-sucedido, exclua o instantâneo de teste foi executado na etapa 6. Para obter mais informações, consulte "Instantâneos de teste de Remove - removeTestStorageSnapshot" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorar o número e tamanho dos instantâneos no volume do disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento desses instantâneos. O comando `ls` não mostra o diretório de instantâneo ou arquivos. O comando de sistema operacional Linux `du` mostra detalhes sobre esses instantâneos de armazenamento porque eles são armazenados nos mesmos volumes. Use o comando com as seguintes opções:

- `du –sh .snapshot`: essa opção fornece um total de todos os instantâneos no diretório de instantâneos.
- `du –sh --max-depth=1`: esta opção lista todos os instantâneos salvos na pasta **.snapshot** e o tamanho de cada um deles.
- `du –hc`: Esta opção fornece o tamanho total usando por todos os instantâneos.

Use estes comandos para certificar-se de que os instantâneos que foram obtidos e armazenados não consumam todo o armazenamento nos volumes.

>[!NOTE]
>Os instantâneos do LUN de inicialização não são visíveis com os comandos anteriores.

### <a name="get-details-of-snapshots"></a>Obter os detalhes de instantâneos
Para obter mais detalhes sobre instantâneos, use o script `azure_hana_snapshot_details`. Você pode executar esse script em qualquer local se houver um servidor ativo no local de recuperação de desastres. O script fornece a saída a seguir dividida por cada volume que contém os instantâneos: 
   * O tamanho do total de instantâneos em um volume
   * Os detalhes a seguir em cada instantâneo nesse volume: 
      - Nome do instantâneo 
      - Criar tempo 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - ID do HANA Backup associada a esse instantâneo, se relevante

Para obter a sintaxe do comando e saídas, consulte "Instantâneos de lista - azure_hana_snapshot_details" na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Reduzir o número de instantâneos em um servidor

Como explicado anteriormente, você pode reduzir o número de certos rótulos de instantâneos que você armazena. Os dois últimos parâmetros do comando para iniciar um instantâneo são o rótulo e o número de instantâneos que você deseja manter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, é o rótulo de instantâneo **dailyhana**. É o número de instantâneos com este rótulo a ser mantido **28**. Ao responder ao consumo de espaço em disco, pode ser útil reduzir o número de instantâneos armazenados. Uma maneira fácil de reduzir o número de instantâneos para 15, por exemplo, é executar o script com o último parâmetro definido como **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se você executar o script com essa configuração, o número de instantâneos, que inclui o novo instantâneo de armazenamento, é 15. Os 15 instantâneos mais recentes são mantidos e os 15 instantâneos mais antigos são excluídos.

 >[!NOTE]
 > Esse script reduzirá o número de instantâneos somente se houver instantâneos com mais de uma hora. O script não exclui os instantâneos que são menos de um hora. Essas restrições estão relacionadas à funcionalidade de recuperação de desastre opcional oferecida.

Se você não deseja mais manter um conjunto de instantâneos com o prefixo de backup **dailyhana** nos exemplos de sintaxe, execute o script com **0** como o número de retenção. Todos os instantâneos que correspondem a esse rótulo são removidos. Remover todos os instantâneos pode afetar os recursos da funcionalidade de recuperação de desastres do HANA em instâncias grandes.

Uma segunda opção para excluir instantâneos específicos é usar o script `azure_hana_snapshot_delete`. Este script foi desenvolvido para excluir um instantâneo ou conjunto de instantâneos usando a ID de backup do HANA conforme encontrada no HANA Studio ou por meio do próprio nome do instantâneo. No momento, a ID de backup só está vinculada aos instantâneos criados para o tipo de instantâneo **hana**. Backups do tipo de instantâneo **logs** e **inicialização** não executam um instantâneo, SAP HANA, portanto, não há nenhuma ID de backup a ser localizada para esses instantâneos. Se o nome do instantâneo for inserido, ele procurará todos os instantâneos em volumes diferentes que correspondam ao nome do instantâneo inserido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter mais informações sobre o script, consulte "Excluir um instantâneo - azure_hana_snapshot_delete" em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Execute o script como usuário **raiz**.

>[!IMPORTANT]
>Se houver dados que existe somente no instantâneo que você planeja excluir, depois que o instantâneo for excluído, que dados serão perdidos para sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauração no nível do arquivo de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de instantâneos **hana** e **logs**, é possível acessar os instantâneos diretamente nos volumes no diretório **.snapshot**. Há um subdiretório para cada um dos instantâneos. Copie cada arquivo no estado em que estava no ponto do instantâneo desse subdiretório para a estrutura de diretório real. 

Na versão atual do script, não há *nenhum* restaurar o script fornecido para a restauração de instantâneo como autoatendimento. Restauração de instantâneo pode ser executada como parte dos scripts de recuperação de desastres de autoatendimento no local de recuperação de desastres durante o failover. Para restaurar um instantâneo desejado dos instantâneos disponíveis existentes, você deve contatar a equipe de operações do Microsoft abrindo uma solicitação de serviço.

>[!NOTE]
>Restauração de arquivo único não funciona para instantâneos do LUN independente do tipo de unidades de instância grande do HANA de inicialização. O **. snapshot** diretório não é exposto no LUN de inicialização. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recuperar para o instantâneo mais recente do HANA

Em um cenário de inatividade na produção, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente do cliente com suporte do Microsoft Azure. É uma questão de alta urgência se dados foram excluídos em um sistema de produção e a única maneira de recuperá-los é restaurar o banco de dados de produção.

Em uma situação diferente, uma recuperação pontual pode ter baixa urgência e ser planejada com dias de antecedência. Você pode planejar essa recuperação com o SAP HANA no Azure em vez de gerar um sinalizador de alta prioridade. Por exemplo, você pode planejar atualizar o software SAP aplicando um novo pacote de aprimoramento. Em seguida, é necessário reverter para um instantâneo que representa o estado antes da atualização de pacote de melhoria.

Antes de enviar a solicitação, é necessário se preparar. O SAP HANA na equipe do Azure pode manipular a solicitação e fornecer os volumes restaurados. Depois, restaure o banco de dados do HANA com base nos instantâneos.

Para as possibilidades para obter um instantâneo restaurado com o novo conjunto de ferramentas, consulte "Como restaurar um instantâneo" nos [guia de recuperação Manual do SAP HANA no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Para preparar para a solicitação, siga estas etapas.

1. Decida qual instantâneo para restaurar. Somente o volume de dados/hana é restaurado, a menos que indicado o contrário. 

1. Desligar a instância do HANA.

   ![Desligar a instância do HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó do banco de dados do HANA. Se os volumes de dados ainda estiverem montados para o sistema operacional, a restauração do instantâneo falhará.

   ![Desmonte os volumes de dados em cada nó do banco de dados HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra uma solicitação de suporte do Azure e inclui instruções sobre a restauração de um instantâneo específico:

   - Durante a restauração: SAP HANA no serviço do Azure pode solicitar que você participe de uma chamada de conferência para coordenar, verificar e confirmar que o instantâneo de armazenamento correto será restaurado. 

   - Depois da restauração: SAP HANA no serviço do Azure notifica você quando o instantâneo de armazenamento é restaurado.

1. Depois que o processo de restauração for concluído, monte novamente todos os volumes de dados.

   ![Montar novamente todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade de obter, por exemplo, arquivos de dados do SAP HANA se recuperou de um instantâneo de armazenamento, está documentada na etapa 7 [guia de recuperação Manual do SAP HANA no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Para restaurar de um backup de instantâneo, consulte [guia de recuperação Manual do SAP HANA no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Se o instantâneo tiver sido restaurado por operações da Microsoft, você não precisa passar para a etapa 7.


### <a name="recover-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
Para restaurar para um determinado ponto no tempo, consulte "Recuperar o banco de dados para o seguinte ponto no tempo" na [guia de recuperação Manual do SAP HANA no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Próximas etapas
- Ver [princípios da recuperação de desastre e preparação](hana-concept-preparation.md).
