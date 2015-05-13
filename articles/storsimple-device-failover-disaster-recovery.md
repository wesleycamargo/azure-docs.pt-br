<properties 
   pageTitle="Como fazer o failover de seu dispositivo StorSimple"
   description="Saiba como fazer o failover de seu dispositivo StorSimple para si mesmo, outro dispositivo físico ou um dispositivo virtual."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/17/2015"
   ms.author="alkohli" />

# Failover e recuperação de desastres para o seu dispositivo StorSimple

## Visão geral

Este tutorial descreve as etapas necessárias para failover de um dispositivo StorSimple no caso de desastre. Um failover permitirá que você migre os dados de um dispositivo de origem no datacenter para outro dispositivo físico ou até mesmo virtual localizado no mesmo ou em um local geográfico diferente. O failover de dispositivo é orquestrado por meio do recurso de recuperação de desastres \(DR\) e é iniciado na página Dispositivos. Esta página exibe em formato de tabela todos os dispositivos StorSimple conectados ao seu serviço StorSimple Manager. Para cada dispositivo, o nome amigável, status, capacidade de provisionamento e máxima, tipo e modelo são exibidos.

![Página Dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

## Recuperação de desastres \(DR\) e failover de dispositivo
Em um cenário de recuperação de desastre \(DR\), o dispositivo principal para de funcionar. Nessa situação, você pode mover os dados de nuvem associados ao dispositivo com falha para outro dispositivo por meio do dispositivo principal como a *origem* e especificando outro dispositivo como o *destino*. Você pode selecionar um ou mais contêineres de volume para migrar para o dispositivo de destino. Esse processo é conhecido como *failover*. Durante o failover, os contêineres de volume do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo de destino.

## Considerações para failover de dispositivo
Em caso de desastre, você pode optar por fazer o failover do seu dispositivo StorSimple:

- Para um dispositivo físico 
- Para ele próprio
- Para um dispositivo virtual

Para o failover de qualquer dispositivo, tenha em mente o seguinte:

- Os pré-requisitos para DR são que todos os volumes em contêineres de volume estejam offline e os contêineres de volume tenham um instantâneo de nuvem associado. 
- Os dispositivos de destino disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os contêineres de volume selecionados. 
- Os dispositivos que estão conectados ao serviço, mas não atendem aos critérios de espaço suficiente não estarão disponíveis como dispositivos de destino.

## Failover para outro dispositivo físico

Execute as seguintes etapas para restaurar seu dispositivo para um dispositivo físico de destino.

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou os instantâneos em nuvem.

1. Na página **Dispositivos**, clique na guia **Contêineres do Volume**.

1. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.

1. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.

1. Na página Dispositivos, clique em **Failover**.

1. No assistente que é aberto, em **Escolher contêiner de volume para failover**:

	1. Na lista de contêineres de volume, selecione os contêineres de volume para failover.

		>[AZURE.NOTE] **Only the volume containers with associated cloud snapshots and offline volumes are displayed.**

	1. Em **Escolher um dispositivo de destino** para os volumes nos contêineres selecionados, selecione um dispositivo de destino na lista suspensa de dispositivos disponíveis. Apenas os dispositivos que têm a capacidade disponível são exibidos na lista suspensa.

	1. Finalmente, revise as configurações de failover em **Confirmar failover**. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Após a conclusão do failover, vá para a página **Dispositivos**.

	1. Selecione o dispositivo que foi usado como o dispositivo de destino para o processo de failover.

	1. Vá para a página **Contêineres de Volume**. Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo devem ser listados.

## Failover usando um único dispositivo

Se você só tiver um único dispositivo e precisa executar um failover, execute as seguintes etapas.

1. Tirar instantâneos de nuvem de todos os volumes em seu dispositivo.

1. Redefina o dispositivo para os padrões de fábrica. Siga as instruções detalhadas em [como redefinir um dispositivo StorSimple para as configurações padrões de fábrica](https://msdn.microsoft.com/library/dn772373.aspx).

1. Configure o seu dispositivo e registre-o novamente no serviço StorSimple Manager.

1. Na página **Dispositivos**, o antigo dispositivo deve aparecer como **Offline**. Os dispositivos registrados recentemente devem aparecer como **Online**.

1. Para o novo dispositivo, conclua a configuração mínima do dispositivo pela primeira vez.
												
	>[AZURE.IMPORTANT]**Se a configuração mínima não for concluída primeiro, a recuperação de desastres falhará devido a um bug na implementação atual. Esse problema será corrigido em uma versão posterior.**

1. Selecione o dispositivo antigo \(status offline\) e clique em **Failover**. No assistente que é apresentado, faça o failover desse dispositivo e especificar o dispositivo de destino como o dispositivo registrado recentemente. Para obter instruções detalhadas, consulte [Failover para outro dispositivo físico](#fail-over-to-another-physical-device).

1. Será criado um trabalho de restauração de dispositivo para que você possa monitorar a partir da página **Trabalhos**.

1. Depois que o trabalho for concluído com êxito, acesse o novo dispositivo e navegue até a página **Contêineres de Volume**. Todos os contêineres de volume do antigo dispositivo agora devem ser migrados para o novo dispositivo.

## Failover para um dispositivo virtual StorSimple

Você deve ter um dispositivo virtual StorSimple criado e configurado antes de executar este procedimento.
 
>[AZURE.NOTE]**Nesta versão, a quantidade de armazenamento com suporte no dispositivo virtual StorSimple é de 30 TB.**

Execute as seguintes etapas para restaurar o dispositivo para um dispositivo virtual do StorSimple de destino.

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou os instantâneos em nuvem.

1. Na página **Dispositivos**, clique na guia **Contêineres do Volume**.

1. Selecione um contêiner de volume para o qual você gostaria de fazer failover para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes neste contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.

1. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.

1. Na página **Dispositivos**, clique em **Failover**.

1. No assistente que é aberto, em **Escolher contêiner de volume para failover**, faça o seguinte:
													
	1. Na lista de contêineres de volume, selecione os contêineres de volume para failover.

		>[AZURE.NOTE] **Only the volume containers with associated cloud snapshots and offline volumes are displayed.**

1. Em **Escolher um dispositivo de destino para os volumes nos contêineres selecionados**, selecione o dispositivo virtual StorSimple na lista suspensa de dispositivos disponíveis. Somente os dispositivos que possuem capacidade suficiente são exibidos na lista suspensa.

1. Finalmente, revise as configurações de failover em Confirmar failover. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Após a conclusão do failover, vá para a página **Dispositivos**.
													
	a. Selecione o dispositivo virtual StorSimple que foi usado como o dispositivo de destino para o processo de failover.
	
	b. Vá para a página **Contêineres de Volume**. Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo devem agora estar listados aqui.


## Consulte também
Depois de realizar o failover, talvez seja necessário:

- [Desativar o dispositivo StorSimple](https://msdn.microsoft.com/library/azure/dn772379.aspx#deactivate)
- [Excluir seu dispositivo StorSimple](https://msdn.microsoft.com/library/azure/dn772379.aspx#delete)

Para obter informações sobre como gerenciar seu dispositivo usando o serviço StorSimple Manager, consulte:

- [Guia do Administrador](https://msdn.microsoft.com/library/dn772401.aspx)


<!--HONumber=52-->
