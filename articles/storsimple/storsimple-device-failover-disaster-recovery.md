<properties 
   pageTitle="Failover e recuperação de desastre do StorSimple | Microsoft Azure"
   description="Saiba como fazer o failover do dispositivo StorSimple para si mesmo, para outro dispositivo físico ou para um dispositivo virtual."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/20/2016"
   ms.author="alkohli" />

# Failover e recuperação de desastres para o seu dispositivo StorSimple

## Visão geral

Este tutorial descreve as etapas necessárias para fazer failover de um dispositivo StorSimple em caso de desastre. Um failover permitirá que você migre os dados de um dispositivo de origem no datacenter para outro dispositivo físico ou até mesmo virtual localizado no mesmo ou em um local geográfico diferente.

O failover de dispositivo é orquestrado por meio do recurso de recuperação de desastres (DR) e é iniciado na página Dispositivos. Esta página exibe em formato de tabela todos os dispositivos StorSimple conectados ao seu serviço StorSimple Manager. Para cada dispositivo, o nome amigável, status, capacidade de provisionamento e máxima, tipo e modelo são exibidos.

![Página Dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

As diretrizes neste tutorial se aplicam a dispositivos físicos e virtuais do StorSimple em todas as versões de software.



## Recuperação de desastres (DR) e failover de dispositivo

Em um cenário de recuperação de desastre (DR), o dispositivo principal para de funcionar. Nessa situação, você pode mover os dados de nuvem associados ao dispositivo com falha para outro dispositivo por meio do dispositivo principal como a *origem* e especificando outro dispositivo como o *destino*. Você pode selecionar um ou mais contêineres de volume para migrar para o dispositivo de destino. Esse processo é conhecido como *failover*. Durante o failover, os contêineres de volume do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo de destino.

## Considerações para failover de dispositivo

Em caso de desastre, você pode optar por fazer o failover do dispositivo StorSimple:

- Para um dispositivo físico 
- Para ele próprio
- Para um dispositivo virtual

Para o failover de qualquer dispositivo, tenha em mente o seguinte:

- Os pré-requisitos para DR são que todos os volumes em contêineres de volume estejam offline e os contêineres de volume tenham um instantâneo de nuvem associado. 
- Os dispositivos de destino disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os contêineres de volume selecionados. 
- Os dispositivos que estão conectados ao serviço, mas não atendem aos critérios de espaço suficiente não estarão disponíveis como dispositivos de destino.

#### Failover de dispositivo em versões de software

Um serviço StorSimple Manager em uma implantação pode ter vários dispositivos físicos e virtuais, todos executando versões de software diferentes. Dependendo da versão do software, os tipos de volume nos dispositivos também podem ser diferentes. Por exemplo, um dispositivo que executa a Atualização 2 ou superior teria volumes em camadas e fixados localmente (com o arquivamento sendo um subconjunto de volumes em camadas). Um dispositivo de pré-atualização 2 por outro lado pode ter volumes em camadas e de arquivamento.

Use a tabela a seguir para determinar se é possível realizar failover para outro dispositivo executando uma versão de software diferente e o comportamento de tipos de volume durante a recuperação de desastre.

| Failover de | Permitido para dispositivo físico | Permitido para dispositivo virtual |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| Atualização 2 para Pré-atualização 1 (versão, 0.1, 0.2, 0.3) | Não | Não |
| Atualização 2 para Atualização 1 (1, 1.1, 1.2) | Sim <br></br>Se usando volumes fixados localmente ou em camadas ou uma combinação dos dois, o failover dos volumes é sempre realizado como em camadas. | Sim<br></br>Se usando volumes fixados localmente, o failover desses volumes é realizado em camadas. |
| Atualização 2 para Atualização 2 (versão posterior) | Sim<br></br>Se usando volumes fixados localmente ou em camadas ou uma combinação dos dois, o failover dos volumes é sempre realizado como o tipo de volume inicial; em camadas como em camadas e fixados localmente como fixados localmente. | Sim<br></br>Se usando volumes fixados localmente, o failover desses volumes é realizado em camadas. |

## Failover para outro dispositivo físico

Execute as seguintes etapas para restaurar seu dispositivo para um dispositivo físico de destino.

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou os instantâneos em nuvem.

1. Na página **Dispositivos**, clique na guia **Contêineres do Volume**.

1. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.

1. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.

1. Na página **Dispositivos**, clique em **Failover**.

1. No assistente que é aberto, em **Escolher contêiner de volume para failover**:

	1. Na lista de contêineres de volume, selecione os contêineres de volume para failover.

		>[AZURE.NOTE] **São exibidos apenas os contêineres de volume com instantâneos de nuvem e volumes offline associados.**

	1. Em **Escolher um dispositivo de destino** para os volumes nos contêineres selecionados, selecione um dispositivo de destino na lista suspensa de dispositivos disponíveis. Apenas os dispositivos que têm a capacidade disponível são exibidos na lista suspensa.

	1. Finalmente, revise as configurações de failover em **Confirmar failover**. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. É criado um trabalho de failover que pode ser monitorado por meio da página **Trabalhos**. Se o contêiner de volume em que você fez failover tiver volumes locais, verá os trabalhos de restauração individuais para cada volume local (não para volumes em camadas) no contêiner. Esses trabalhos de restauração podem levar algum tempo para ser concluídos. É provável que o trabalho de failover possa ser concluído anteriormente. Observe que esses volumes terão garantias locais somente depois que os trabalhos de restauração forem concluídos. Após a conclusão do failover, vá para a página **Dispositivos**.

	1. Selecione o dispositivo que foi usado como o dispositivo de destino para o processo de failover.

	1. Vá para a página **Contêineres de Volume**. Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo devem ser listados.

## Failover usando um único dispositivo

Se você só tiver um único dispositivo e precisa executar um failover, execute as seguintes etapas.

1. Tirar instantâneos de nuvem de todos os volumes em seu dispositivo.

1. Redefina o dispositivo para os padrões de fábrica. Siga as instruções detalhadas em [como redefinir um dispositivo StorSimple para as configurações padrões de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

1. Configure o seu dispositivo e registre-o novamente no serviço StorSimple Manager.

1. Na página **Dispositivos**, o antigo dispositivo deve aparecer como **Offline**. Os dispositivos registrados recentemente devem aparecer como **Online**.

1. Para o novo dispositivo, conclua a configuração mínima do dispositivo pela primeira vez.
												
	>[AZURE.IMPORTANT] **Se a configuração mínima não for concluída primeiro, a recuperação de desastres falhará devido a um bug na implementação atual. Esse problema será corrigido em uma versão posterior.**

1. Selecione o dispositivo antigo (status offline) e clique em **Failover**. No assistente que é apresentado, faça o failover desse dispositivo e especificar o dispositivo de destino como o dispositivo registrado recentemente. Para obter instruções detalhadas, consulte [Failover para outro dispositivo físico](#fail-over-to-another-physical-device).

1. Será criado um trabalho de restauração de dispositivo para que você possa monitorar a partir da página **Trabalhos**.

1. Depois que o trabalho for concluído com êxito, acesse o novo dispositivo e navegue até a página **Contêineres de Volume**. Todos os contêineres de volume do antigo dispositivo agora devem ser migrados para o novo dispositivo.

## Failover para um dispositivo virtual StorSimple

Você deve ter um dispositivo virtual StorSimple criado e configurado antes de executar este procedimento. Se executar a Atualização 2, considere usar um dispositivo virtual 8020 para a recuperação de desastres que tem 64 TB e usa o Armazenamento Premium.
 
Execute as seguintes etapas para restaurar o dispositivo para um dispositivo virtual do StorSimple de destino.

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou os instantâneos em nuvem.

1. Na página **Dispositivos**, clique na guia **Contêineres do Volume**.

1. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.

1. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.

1. Na página **Dispositivos**, clique em **Failover**.

1. No assistente que é aberto, em **Escolher contêiner de volume para failover**, faça o seguinte:
													
	a. Na lista de contêineres de volume, selecione os contêineres de volume para failover.

	>[AZURE.NOTE] **São exibidos apenas os contêineres de volume com instantâneos de nuvem e volumes offline associados.**

	b. Em **Escolher um dispositivo de destino para os volumes nos contêineres selecionados**, selecione o dispositivo virtual StorSimple na lista suspensa de dispositivos disponíveis. Somente os dispositivos que possuem capacidade suficiente são exibidos na lista suspensa.
	

1. Finalmente, revise as configurações de failover em **Confirmar failover**. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Após a conclusão do failover, vá para a página **Dispositivos**.
													
	a. Selecione o dispositivo virtual StorSimple que foi usado como o dispositivo de destino para o processo de failover.
	
	b. Vá para a página **Contêineres de Volume**. Agora devem estar listados todos os contêineres de volume, juntamente com os volumes do antigo dispositivo.

![Vídeo disponível](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como é possível restaurar um dispositivo físico que passou por failover em um dispositivo virtual na nuvem, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## BCDR (recuperação de desastre de continuidade de negócios)

Um cenário de BCDR (recuperação de desastre de continuidade de negócios) ocorre quando todo o datacenter do Azure para de funcionar. Isso pode afetar o serviço StorSimple Manager e os dispositivos StorSimple associados.

Se houver dispositivos StorSimple que foram registrados antes da ocorrência de um desastre, talvez eles precisem passar por uma redefinição de fábrica. Após o desastre, o dispositivo StorSimple será mostrado como offline. O dispositivo StorSimple deve ser excluído do portal, e uma redefinição de fábrica deve ser feita, seguida de um novo registro.


## Próximas etapas

- Depois de realizar um failover, talvez seja necessário [desativar ou excluir seu dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

- Para obter informações sobre como usar o serviço StorSimple Manager, acesse [Usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
 

<!---HONumber=AcomDC_0128_2016-->