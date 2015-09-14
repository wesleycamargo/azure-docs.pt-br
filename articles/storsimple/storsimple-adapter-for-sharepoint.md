<properties 
   pageTitle="Adaptador do StorSimple para SharePoint | Microsoft Azure"
	description="Descreve como instalar e configurar o Adaptador StorSimple para o SharePoint em um farm de servidores do SharePoint."
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
	ms.date="08/27/2015"
	ms.author="v-sharos"/>

# Instalar e configurar o Adaptador StorSimple para o SharePoint

## Visão geral

O Adaptador StorSimple para SharePoint é um componente que permite fornecer armazenamento flexível do Microsoft Azure StorSimple e proteção de dados para farms de servidores do SharePoint. Você pode usar o adaptador para mover o conteúdo do BLOB (objeto binário grande) de bancos de dados de conteúdo do SQL Server para o dispositivo de armazenamento de nuvem híbrida do Microsoft Azure StorSimple.

O adaptador StorSimple para SharePoint funciona como um provedor de RBS (armazenamento de BLOB remoto) e usa o recurso de armazenamento de BLOB remoto do SQL Server para armazenar o conteúdo do SharePoint não estruturado (na forma de BLOBs) em um servidor de arquivos apoiado por um dispositivo StorSimple.

>[AZURE.NOTE]O adaptador StorSimple para SharePoint dá suporte ao RBS (Remote BLOB Storage) do SharePoint Server 2010 . Ele não dá suporte a EBS (External BLOB Storage) do SharePoint Server 2010.

- Para baixar o adaptador StorSimple para SharePoint, vá para [Adaptador StorSimple para SharePoint][1] no Centro de Download da Microsoft.

- Para obter informações sobre como planejar para RBS e as limitações de RBS limitações, vá para [Decidindo usar RBS no SharePoint 2013][2] ou [Planejar para RBS (SharePoint Server 2010)][3].

O restante desta visão geral descreve resumidamente a função do Adaptador StorSimple para SharePoint e os limites de desempenho e capacidade do SharePoint que você deve conhecer antes de instalar e configurar o adaptador. Depois de analisar essas informações, vá para [Instalação do Adaptador StorSimple para SharePoint](#storsimple-adapter-for-sharepoint-installation) para começar a configurar o adaptador.

### Benefícios do Adaptador StorSimple para SharePoint

Em um site do SharePoint, o conteúdo é armazenado como dados BLOB não estruturados em um ou mais bancos de dados. Por padrão, esses bancos de dados são hospedados em computadores que executam o SQL Server e estão localizados no farm de servidores do SharePoint. Os BLOBs podem aumentar rapidamente de tamanho, consumindo grandes quantidades de armazenamento local. Por esse motivo, convém encontrar outra solução de armazenamento de menor custo. O SQL Server fornece uma tecnologia chamada RBS (Remote Blob Storage), que permite armazenar o conteúdo do BLOB no sistema de arquivos fora do banco de dados do SQL Server. Com o RBS, os BLOBs podem residir no sistema de arquivos no computador que está executando o SQL Server, ou ser armazenados no sistema de arquivos em outro computador do servidor.

O RBS requer que você use um provedor RBS, como o Adaptador StorSimple para SharePoint, para habilitar o RBS no SharePoint. O adaptador StorSimple para SharePoint funciona com o RBS, permitindo que você mova os BLOBs para um servidor apoiado pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple então armazena os dados BLOB localmente ou na nuvem, com base no uso. BLOBs que são muito ativos (normalmente conhecidos como Camada 1 ou dados ativos) residem localmente. Os dados menos ativos e dados de arquivamento residem na nuvem. Depois de habilitar RBS em um banco de dados de conteúdo, todo novo conteúdo do BLOB criado no SharePoint é armazenado no dispositivo StorSimple, e não no banco de dados.

A implementação de RBS do Microsoft Azure StorSimple fornece os seguintes benefícios:

- Ao mover o conteúdo do BLOB para um servidor separado, você pode reduzir a carga de consulta no SQL Server, o que pode melhorar a capacidade de resposta do SQL Server. 

- O Azure StorSimple usa eliminação de duplicação e compactação para reduzir o tamanho dos dados.

- O Azure StorSimple fornece proteção de dados na forma de instantâneos locais e em nuvem. Além disso, se você colocar o banco de dados no dispositivo StorSimple, pode fazer backup do banco de dados de conteúdo e BLOBs juntos de uma forma consistente de falha. (Mover o banco de dados de conteúdo para o dispositivo tem suporte apenas para o dispositivo de séries StorSimple 8000. Esse recurso não tem suporte para a série 5000 ou 7000.)

- O Azure StorSimple inclui recursos de recuperação de desastres, inclusive failover, recuperação de arquivo e volume (incluindo recuperação de teste) e restauração rápida de dados.

- Você pode usar o software de recuperação de dados, como Kroll Ontrack PowerControls, com os instantâneos do StorSimple de dados BLOB para executar a recuperação no nível do item de conteúdo do SharePoint. (Esse software de recuperação de dados é uma compra separada.)

- O adaptador StorSimple para SharePoint conecta-se ao portal de Administração Central do SharePoint, permitindo gerenciar toda a solução do SharePoint em um local central.

Mover o conteúdo BLOB para o sistema de arquivos pode proporcionar outras economias de custos e benefícios. Por exemplo, usar o RBS pode reduzir a necessidade de armazenamento de camada 1 e, porque reduz o banco de dados de conteúdo, o RBS pode reduzir o número de bancos de dados necessários no farm de servidores do SharePoint. No entanto, outros fatores, como limites de tamanho do banco de dados e a quantidade de conteúdo não RBS, também podem afetar os requisitos de armazenamento. Para obter mais informações sobre os custos e os benefícios de usar o RBS, consulte [Planejar para RBS (SharePoint Foundation 2010)][4] e [Decidindo usar o RBS no SharePoint 2013][5].

### Limites de capacidade e desempenho

Antes de considerar o uso de RBS em sua solução do SharePoint, conheça os limites de desempenho e capacidade testados do SharePoint Server 2010 e do SharePoint Server 2013 e como esses limites se relacionam com desempenho aceitável. Para obter mais informações, consulte Limites de software e limites para o SharePoint 2013.

Examina o seguinte antes de configurar o RBS:

- Certifique-se de que o tamanho total do conteúdo (o tamanho de um banco de dados de conteúdo) mais o tamanho de todos os BLOBs externalizados associados não exceda o limite de tamanho RBS compatível com o SharePoint. Esse limite é de 200 GB. 

    **Para medir o tamanho do BLOB e do banco de dados de conteúdo**

     1. Execute esta consulta no WFE de Administração Central. Inicie o Shell de Gerenciamento do SharePoint e, em seguida, digite o seguinte comando do Windows PowerShell para obter o tamanho dos bancos de dados de conteúdo:

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         Esta etapa obtém o tamanho do banco de dados de conteúdo no disco.

     2. Execute uma das seguintes consultas SQL no SQL Management Studio na caixa do SQL Server em cada banco de dados de conteúdo e adicione o resultado ao número obtido na etapa 1.

        Nos bancos de dados de conteúdo do SharePoint 2013, digite:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        Nos bancos de dados de conteúdo do SharePoint 2010, digite:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        Esta etapa obtém o tamanho dos BLOBs que foram externalizados.

- É recomendável armazenar todo o conteúdo do BLOB e do banco de dados localmente no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para alta disponibilidade. Colocar os bancos de dados de conteúdo e BLOBs no dispositivo StorSimple fornece alta disponibilidade.

    Use práticas recomendadas de migração de SQL Server tradicionais para mover o banco de dados de conteúdo para o dispositivo StorSimple. Mova o banco de dados somente depois de todo o conteúdo do BLOB do banco de dados ter sido movido para o compartilhamento de arquivos via RBS. Caso opte por mover o banco de dados de conteúdo para o dispositivo StorSimple, é recomendável configurar o armazenamento de banco de dados de conteúdo no dispositivo como um volume primário.

- No Microsoft Azure StorSimple, não há nenhuma maneira de garantir que o conteúdo armazenado localmente no dispositivo StorSimple não será colocado em camadas no armazenamento em nuvem do Microsoft Azure. Para garantir que o banco de dados de conteúdo permaneça no dispositivo StorSimple e não seja movido para o Microsoft Azure (o que poderia afetar negativamente os tempos de resposta de transação do SharePoint), é importante compreender e gerenciar as outras cargas de trabalho no dispositivo StorSimple. Não é recomendável configurar um dispositivo StorSimple para hospedar cargas de trabalho que tenham uma alta taxa de gravações de dados se o dispositivo já estiver hospedando cargas de trabalho do banco de dados de conteúdo do SharePoint e cargas de trabalho de compartilhamento de arquivos do SharePoint.

- Se você não armazenar os bancos de dados de conteúdo no dispositivo StorSimple, use as práticas recomendadas tradicionais de alta disponibilidade do SQL Server que ofereçam suporte a RBS. O clustering do SQL Server dá suporte a RBS, enquanto o espelhamento do SQL Server, não.

>[AZURE.WARNING]Se você não tiver habilitado RBS, não é recomendável mover o banco de dados de conteúdo para o dispositivo StorSimple. Essa é uma configuração não testada.
 
## Adaptador StorSimple para instalação do SharePoint

Antes de instalar o adaptador StorSimple para SharePoint, você deve configurar o dispositivo StorSimple e certificar-se de que o farm do SharePoint Server e a instanciação do SQL Server atendam a todos os pré-requisitos. Este tutorial descreve os requisitos de configuração, bem como os procedimentos para instalar e atualizar o Adaptador StorSimple para SharePoint.

## Configurar pré-requisitos

Antes de instalar o adaptador StorSimple para SharePoint, garanta que o dispositivo StorSimple, o farm do SharePoint Server e a instanciação do SQL Server atendam a todos os pré-requisitos.

### Requisitos do sistema

O Adaptador StorSimple para SharePoint funciona com os produtos de hardware e software a seguir:

- Sistema operacional com suporte – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2 

- Versões do SharePoint com suporte – SharePoint Server 2010 ou SharePoint Server 2013

- Versões do SQL Server com suporte – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition ou SQL Server 2012 Enterprise Edition

- Dispositivos do StorSimple Versões do SQL Server com suporte – StorSimple 8000 series, StorSimple 7000 series ou StorSimple 5000 series.

### Pré-requisitos de configuração do dispositivo StorSimple

O dispositivo StorSimple é um dispositivo de bloco e, como tal, requer um servidor de arquivos no qual os dados possam ser hospedados. É recomendável usar um servidor separado, em vez de um servidor existente do farm do SharePoint. Esse servidor de arquivos deve estar na mesma rede local (LAN) que o computador SQL Server que hospeda os bancos de dados de conteúdo.

>[AZURE.TIP]
>
>- Se você configurar seu farm do SharePoint para alta disponibilidade, deverá implantar o servidor de arquivos para alta disponibilidade também.
>
>- Se você não armazenar o banco de dados de conteúdo no dispositivo StorSimple, use práticas recomendadas tradicionais de alta disponibilidade que ofereçam suporte a RBS. O clustering do SQL Server dá suporte a RBS, enquanto o espelhamento do SQL Server, não.

Certifique-se de que seu dispositivo StorSimple esteja configurado corretamente e que os volumes apropriados para dar suporte à implantação do SharePoint estão configurados e acessíveis do computador do SQL Server. Vá para [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough.md) se ainda não tiver implantado e configurado seu dispositivo StorSimple. Anote o endereço IP do dispositivo StorSimple; você precisará dele durante a instalação do Adaptador StorSimple para SharePoint.

Além disso, certifique-se de que o volume a ser usado para externalização do BLOB cumpra os seguintes requisitos:

- O volume deve ser formatado com um tamanho de unidade de alocação de 64 KB.

- O front-end da web (WFE) e os servidores de aplicativos devem poder acessar o volume por meio de um caminho de UNC (convenção de nomenclatura Universal).

- O farm de servidores do SharePoint deve ser configurado para gravar no volume.

>[AZURE.NOTE]Depois de instalar e configurar o adaptador, toda externalização do BLOB deve passar pelo dispositivo StorSimple (o dispositivo apresentará os volumes ao SQL Server e gerenciará os níveis de armazenamento). Você não pode usar nenhum outro destino para externalização do BLOB.
 
Se você planeja usar o Gerenciador de instantâneos StorSimple para capturar instantâneos dos dados do BLOB e do banco de dados, instale o Gerenciador de instantâneos StorSimple no servidor de banco de dados para que ele possa usar o Serviço Gravador SQL para implementar o Windows Volume Shadow Copy Service (VSS).

>[AZURE.IMPORTANT]O Gerenciador de instantâneos StorSimple não tem suporte para o Gravador VSS do SharePoint e não é possível tirar instantâneos consistentes com aplicativos de dados do SharePoint. Em um cenário do SharePoint, o Gerenciador de instantâneos StorSimple fornece somente backups consistentes com falhas.
 
## Pré-requisitos de configuração de farm do SharePoint

Certifique-se de que seu farm do SharePoint Server esteja configurado corretamente da seguinte maneira:

- Verifique se seu farm de servidores do SharePoint está em um estado íntegro e verifique o seguinte: 

- Todos os servidores de aplicativos e WFE do SharePoint registrados no farm estão em execução e podem ser alcançados pelo ping do servidor no qual você instalará o Adaptador StorSimple para SharePoint.

- O serviço de Timer do SharePoint (SPTimerV3 ou SPTimerV4) está em execução em cada servidor WFE e servidor de aplicativos.

- O serviço de Timer do SharePoint e o pool de aplicativos do IIS em que o site de Administração Central do SharePoint está em execução tem privilégios administrativos.

- Certifique-se de que o IE ESC (Internet Explorer Enhanced Security Context) esteja desabilitado. Siga estas etapas para desabilitar o IE ESC:

    1. Feche todas as instâncias do Internet Explorer.

    2. Inicie o Gerenciador do Servidor.

    3. No painel esquerdo, clique em **Servidor Local**.

    4. No painel direito, próximo a **Configuração de Segurança Reforçada do IE**, clique em **Ligado**.

    5. Em **Administradores**, clique em **Desligado**.

    6. Clique em **OK**.

## Pré-requisitos do RBS (Remote BLOB Storage)

Certifique-se de que você esteja usando uma versão com suporte do SQL Server. Somente as versões a seguir têm suporte e podem usar RBS:

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

BLOBs podem ser externalizados somente nos volumes que o dispositivo StorSimple apresentar ao SQL Server. Não há suporte para outros destinos para externalização de BLOB.

Quando você tiver concluído todas as etapas de configuração de pré-requisitos, vá para [Instalar o Adaptador StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## Instale o Adaptador StorSimple para SharePoint

Use as seguintes etapas para instalar o Adaptador StorSimple para SharePoint. Se você estiver reinstalando o software, consulte [Atualizar ou reinstalar o Adaptador StorSimple para SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende do número total de bancos de dados do SharePoint no farm do SharePoint Server.

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## Configurar RBS

Depois de instalar o Adaptador StorSimple para SharePoint, configure RBS conforme descrito no procedimento a seguir.

>[AZURE.TIP]O Adaptador StorSimple para SharePoint conecta-se à página Administração Central do SharePoint, permitindo ao RBS ser habilitado ou desabilitado em cada banco de dados de conteúdo no farm do SharePoint. No entanto, habilitar ou desabilitar RBS no banco de dados provoca uma redefinição do IIS, que, dependendo da configuração do farm, pode momentaneamente interromper a disponibilidade do WFE (front-end da Web) do SharePoint. (Fatores como o uso de um balanceador de carga de front-end, a carga de trabalho atual do servidor e assim por diante podem limitar ou eliminar essa interrupção). Para proteger os usuários contra uma interrupção, recomendamos habilitar ou desabilitar RBS somente durante uma janela de manutenção planejada.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## Configurar a coleta de lixo

Quando objetos são excluídos de um site do SharePoint, eles não são automaticamente excluídos do volume de armazenamento RBS. Em vez disso, um programa assíncrono de manutenção em segundo plano exclui os BLOBs órfãos do repositório de arquivo. Os administradores do sistema podem agendar esse processo para ser executado periodicamente ou iniciá-lo sempre que necessário.

Este programa de manutenção (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) é instalado automaticamente em todos os servidores SharePoint WFE e servidores de aplicativos quando você habilita RBS. O programa é instalado no seguinte local: <boot drive>:\\Program Files\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer\\

Para obter informações sobre como configurar e usar o programa de manutenção, consulte [Manter RBS em SharePoint Server 2013][8].

>[AZURE.IMPORTANT]O programa de manutenção do RBS utiliza muitos recursos. Você deve agendá-lo para execução apenas durante períodos de atividade leve no farm do SharePoint.

### Excluir BLOBs órfãos imediatamente

Se você precisar excluir BLOBs órfãos imediatamente, pode usar as instruções a seguir. Observe que essas instruções são um exemplo de como isso pode ser feito em um ambiente do SharePoint 2013 com os seguintes componentes:

- O nome do banco de dados de conteúdo é WSS\_Content.
- O nome do SQL Server é SHRPT13-SQL12\\SHRPT13.
- O nome do aplicativo Web é SharePoint-80.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## Atualize ou reinstale o Adaptador StorSimple para SharePoint

Use o procedimento a seguir para atualizar o servidor do SharePoint e, em seguida, reinstalar o adaptador StorSimple para SharePoint ou para simplesmente atualizar ou reinstalar o adaptador em um farm do SharePoint Server existente.

>[AZURE.IMPORTANT]Examine as seguintes informações antes de tentar atualizar o software do SharePoint e/ou atualizar ou reinstalar o Adaptador StorSimple para SharePoint:
>
>- Todos os arquivos que foram movidos anteriormente para o armazenamento externo via RBS não estarão disponíveis até que a reinstalação seja concluída e o recurso RBS esteja habilitado novamente. Para limitar o impacto ao usuário, execute qualquer atualização ou reinstalação durante uma janela de manutenção planejada.
>
>- O tempo necessário para atualizar/reinstalar pode variar conforme o número total de bancos de dados do SharePoint no farm de servidores do SharePoint.
>
>- Após a conclusão da reinstalação/atualização, você precisa habilitar o RBS para os bancos de dados de conteúdo. Consulte [Configurar RBS](#configure-rbs) para obter mais informações.
>
>- Se você estiver configurando um RBS para um farm do SharePoint que tenha um número muito grande de bancos de dados (mais de 200), a página **Administração Central do SharePoint** poderá atingir o tempo limite. Se isso ocorrer, atualize a página. Isso não afeta o processo de configuração.

## Próximas etapas

[Saiba mais sobre o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/pt-BR/library/ff943565.aspx

<!---HONumber=September15_HO1-->