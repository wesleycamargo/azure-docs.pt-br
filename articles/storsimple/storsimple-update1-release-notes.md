<properties 
    pageTitle="Notas de versão do StorSimple série 8000 Atualização 1 | Microsoft Azure"
    description="Descreve os novos recursos, problemas e soluções alternativas da Atualização 1 do StorSimple série 8000."
    services="storsimple"
    documentationCenter="NA"
    authors="alkohli"
    manager="carolz"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="08/19/2015"
    ms.author="alkohli" />

# Notas de versão da Atualização 1 do StorSimple série 8000  

## Visão geral

As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos da Atualização 1 do StorSimple série 8000. Elas também contêm uma lista das atualizações de firmware e software do StorSimple incluídas nesta versão. Esta é a primeira versão principal depois que a versão de lançamento do StorSimple série 8000 foi disponibilizada em julho de 2014.

Essa atualização altera o software do dispositivo para a Atualização 1 do StorSimple série 8000. Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple. Para saber mais, veja como [instalar a Atualização 1 no seu dispositivo StorSimple](storsimple-install-update-1.md).

Examine as informações contidas nas notas de versão antes de implantar as atualizações em sua solução do StorSimple.

>[AZURE.IMPORTANT]
> 
- Um patch crítico, Atualização 1.1, foi lançada em 23 de junho. Esse patch resolve um problema no mecanismo de backup. Se você aplicou a Atualização 1 antes de 23 de junho e estiver usando software versão **6.3.9600.17491**, certifique-se de aplicar essa atualização crítica para evitar problemas com backups. Depois de instalar a atualização, a versão do software será alterada para **6.3.9600.17521**.
- Se você criou um dispositivo virtual entre 27 de maio e 10 de julho que está na versão de software 6.3.9600.17481, crie um novo dispositivo virtual e o faça failover de todos os volumes do dispositivo virtual antigo para o novo. (Isso ocorre porque o dispositivo virtual mais antigo não pode ser atualizado.) Se você não criar um novo dispositivo virtual, poderá ver os backups começarem a falhar. Para procedimentos de recuperação de desastres e failover, vá até [Failover e recuperação de desastres para seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).
- Use o serviço StorSimple Manager, e não o Windows PowerShell para StorSimple, para instalar a Atualização 1.
- Esta versão também contém as atualizações de firmware de disco que só podem ser aplicadas quando o dispositivo está no modo de Manutenção. Essas são as atualizações com interrupção que podem resultar em tempo de inatividade para seu dispositivo. Você pode aplicar essas atualizações durante a manutenção planejada.
- São necessárias cerca de 5 a 10 horas para instalar essa atualização (incluindo as atualizações do Windows). 
- Para novas versões, talvez você não veja atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Procure atualizações em poucos dias novamente, uma vez que elas serão disponibilizadas em breve.

## Novidades na Atualização 1

Esta atualização contém os seguintes recursos e aprimoramentos novos:

- **Migração de dispositivos séries 5000-7000 para dispositivos da série 8000** – esta versão apresenta um novo recurso de migração que permite que usuários de dispositivos StorSimple séries 5000-7000 migrem seus dados para um dispositivo físico StorSimple série 8000 ou um dispositivo virtual 1100. O recurso de migração tem duas proposições de valor fundamentais:                                                                  

    - **Continuidade dos negócios**, permitindo a migração dos dados existentes nos dispositivos séries 5000-7000 para dispositivos série 8000.
    - **Ofertas de recursos aprimorados dos dispositivos série 8000**, como o gerenciamento centralizado eficiente de vários dispositivos por meio do serviço StorSimple Manager, uma classe melhor de hardware e firmware atualizado, dispositivos virtuais, mobilidade de dados e recursos no mapa futuro.

    Consulte o [guia de migração](http://www.microsoft.com/download/details.aspx?id=47322) para obter detalhes sobre como migrar um dispositivo StorSimple série 5000-7000 para um dispositivo série 8000.

- **Disponibilidade no Portal Governamental do Azure** – Agora, o StorSimple está disponível no portal Governamental do Azure. Veja como [implantar um dispositivo StorSimple no Portal Governamental do Azure](storsimple-deployment-walkthrough-gov.md).

- **Suporte para outros provedores de serviços de nuvem** – Os outros provedores de serviços de nuvem com suporte são Amazon S3, Amazon S3 com RRS, HP e OpenStack (beta).

- **Atualização para as APIs de armazenamento mais recentes** – Com esta versão, o StorSimple foi atualizado para as APIs de serviço de armazenamento do Azure mais recentes. Os dispositivos StorSimple série 8000 que executam GA estão usando versões das APIs de serviço de armazenamento do Azure anteriores a 12 de fevereiro de 2012. Como mencionado no [anúncio sobre remoção de versões de serviço de armazenamento](http://azure.microsoft.com/blog/2014/08/04/microsoft-azure-storage-service-version-removal/), até 10 de dezembro de 2015, essas APIs serão preteridas. É imperativo que você aplique a Atualização 1 do StorSimple série 8000 antes de 9 de dezembro de 2015. Se você não conseguir fazer isso, os dispositivos StorSimple deixarão de funcionar corretamente.

- **Suporte para ZRS (armazenamento com redundância de zona)** – com a atualização para a versão mais recente das APIs de armazenamento, o StorSimple série 8000 oferecerá suporte para ZRS (armazenamento com redundância de zona), além de LRS (armazenamento com redundância local) e GRS (armazenamento com redundância geográfica). Consulte o [artigo sobre opções de redundância de armazenamento do Azure](../storage/storage-redundancy.md) para obter detalhes do ZRS.

- **Experiência de implantação inicial e atualização aprimorada** – nesta versão, os processos de instalação e atualização foram aprimorados. A instalação por meio do assistente de instalação foi aprimorada para informar ao usuário se a configuração de rede e as configurações do firewall estão incorretas. Cmdlets de diagnóstico adicionais foram fornecidos para ajudar a solucionar problemas de rede do dispositivo. Consulte o [artigo sobre solução de problemas de implantação](storsimple-troubleshoot-deployment.md) para saber mais sobre os novos cmdlets de diagnóstico usados para solucionar problemas.

## Problemas corrigidos na Atualização 1

A tabela a seguir fornece um resumo dos problemas que foram corrigidos nesta atualização.

| Nº | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell para StorSimple | Quando um usuário acessava remotamente o dispositivo StorSimple por meio do Windows PowerShell para StorSimple e depois iniciava o assistente de instalação, uma falha ocorria assim que o IP 0 de Dados era inserido. Agora, esse bug foi corrigido na Atualização 1. | Sim | Sim |
| 2 | Redefinição de fábrica | Em alguns casos, quando você executava uma redefinição de fábrica, o dispositivo StorSimple travava e exibia esta mensagem: **a redefinição de fábrica está em andamento (fase 8)**. Isso acontecia se você pressionasse CTRL+C enquanto o cmdlet estava em andamento. Agora esse bug foi corrigido.| Sim | Não |
| 3 | Redefinição de fábrica | Depois de uma redefinição de fábrica de controlador duplo com falha, você podia continuar com o registro do dispositivo. Isso resultava em uma configuração de sistema sem suporte. Na Atualização 1, uma mensagem de erro é mostrada e o registro é bloqueado em um dispositivo que tenha uma redefinição de fábrica com falha. | Sim | Não |
| 4 | Redefinição de fábrica | Em alguns casos, foram gerados alertas de incompatibilidade falso positivos. Os alertas de incompatibilidade incorretos não serão mais gerados em dispositivos com a Atualização 1 em execução. | Sim | Não |
| 5 | Redefinição de fábrica | Se uma redefinição de fábrica fosse interrompida antes da conclusão, o dispositivo entrava no modo de recuperação e não permitia que você acessasse o Windows PowerShell para StorSimple. Agora esse bug foi corrigido. | Sim | Não |
| 6 | Recuperação de desastre | Um bug de recuperação de desastre foi corrigido no qual a DR falhava durante a descoberta de backups no dispositivo de destino. | Sim | Sim |
| 7 | LEDs de monitoramento | Em determinadas circunstâncias, os LEDs de monitoramento na parte posterior do dispositivo não indicavam o status correto. O LED azul ficava apagado. Os LEDs de DADOS 0 e 1 ficavam piscando mesmo quando essas interfaces não estavam configuradas. O problema foi corrigido e os LEDs de monitoramento agora indicam o status correto. | Sim | Não |
| 8 | Interfaces de rede | Nas versões anteriores, um dispositivo StorSimple configurado com um gateway não roteável podia ficar offline. Nesta versão, a métrica de roteamento para Dados 0 foi feita a menor possível; portanto, mesmo que outras interfaces de rede estiverem habilitadas para a nuvem, todo o tráfego de nuvem do dispositivo será roteado por meio de Dados 0. | Sim | Sim | 
| 9 | Backups | Um bug na Atualização 1 (versão de software 6.3.9600.17491) que causou a falha de backups após 24 dias foi corrigido na versão da Atualização 1.1 da versão do patch (versão de software 6.3.9600.17521). | Sim | Sim |

## Problemas conhecidos na Atualização 1

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Nº | Recurso | Problema | Comentários/soluções alternativas | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disco | Em casos raros, se a maioria dos discos no invólucro de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quórum de disco, em seguida, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados. | Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. | Sim | Não |
| 2 | ID de controlador incorreta | Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares. Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema. | Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída. | Sim | Não |
| 3 | Contas de armazenamento | Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | Sim | Sim |
| 4 | Failover de dispositivo | Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino. O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal de Gerenciamento. | | Sim | Não |
| 5 | Instalação | Durante o Adaptador StorSimple para instalação do SharePoint, você precisa fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | | Sim | Não |
| 6 | Proxy Web | Se a configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de serviço do dispositivo será afetada e o dispositivo ficará offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo. | Verifique se a URL do proxy Web possui HTTP como o protocolo especificado. Para obter mais informações, visite [Configurar proxy da Web para seu dispositivo](storsimple-configure-web-proxy.md). | Sim | Não |
| 7 | Proxy Web | Ao configurar e habilitar o proxy Web em um dispositivo registrado, você precisará reiniciar o controlador ativo em seu dispositivo. | | Sim | Não |
| 8 | Latência de nuvem alta e alta carga de trabalho de E/S | Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito altas (ordem de segundos) e alta carga de trabalho de E/S, os volumes do dispositivo entram em um estado degradado e as E/Ss podem falhar com o erro "o dispositivo não está pronto". | Você precisará reiniciar os controladores de dispositivo manualmente ou executar um failover do dispositivo para se recuperar dessa situação. | Sim | Não |
| 9 | PowerShell do Azure | Quando você usa o cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential &\#124; Select-Object -First 1 -Wait** para selecionar o primeiro objeto para que possa criar um novo objeto **VolumeContainer**, o cmdlet retorna todos os objetos. | Coloque o cmdlet entre parênteses da seguinte maneira: **(Get-Azure-StorSimpleStorageAccountCredential) &\#124; Select-Object -First 1 -Wait** | Sim | Sim |
| 10| Migração | Quando contêineres de vários volumes forem passados para a migração, o ETA do backup mais recente será preciso apenas para o contêiner do primeiro volume. Além disso, a migração paralela será iniciada depois que os primeiros 4 backups no primeiro contêiner de volume forem migrados. | É recomendável que você migre um contêiner de volume por vez. | Sim | Não |
| 11| Migração | Após a restauração, os volumes não são adicionados à política de backup ou ao grupo de discos virtuais. | Você terá que adicionar esses volumes a uma política de backup para criar backups. | Sim | Sim |
| 12| Migração | Depois que a migração for concluída, o dispositivo série 5000/7000 não deverá acessar os contêineres de dados migrados. | É recomendável que você exclua os contêineres de dados migrados quando a migração estiver concluída e confirmada. | Sim | Não |
| 13| Clonagem e recuperação de desastre | Um dispositivo StorSimple executando a Atualização 1 não pode clonar ou executar a recuperação de desastre em um dispositivo que executa o software anterior à Atualização 1. | Você precisará atualizar o dispositivo de destino para a Atualização 1 para permitir essas operações | Sim | Sim |
| 14 | Migração | O backup de configuração para a migração poderá falhar em um dispositivo da série 5000-7000 quando houver grupos de volumes sem volumes associados. | Exclua todos os grupos de volumes vazios sem volumes associados e repita o backup de configuração.| Sim | Não |

## Atualizações de dispositivo físico na Atualização 1

Quando essas atualizações são aplicadas a um dispositivo físico, a versão do software é alterada para 6.3.9600.17521.

## Controlador SCSI (SAS) anexado em série e atualizações na Atualização 1

Esta versão atualiza o driver e o firmware no controlador SAS de seu dispositivo físico. Ela também atualiza o firmware de disco no seu dispositivo.
 
- Para saber mais sobre a atualização do controlador SAS, confira [Atualização 1 para controladores SAS LSI no dispositivo do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 

- Para saber mais sobre a atualização de firmware, confira [Atualização 1 de firmware para o dispositivo do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063414).

- Para saber mais sobre a atualização de firmware de disco, confira [Atualização 1 de firmware de disco para o dispositivo do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416).
 
## Atualizações de dispositivo virtual na Atualização 1

Esta atualização não pode ser aplicada ao dispositivo virtual. No entanto, os dispositivos virtuais criados após 10 de julho estarão automaticamente nesta versão.

## Próximas etapas

- [Instalar a Atualização 1 no seu dispositivo](storsimple-install-update-1.md).
 

<!---HONumber=August15_HO8-->