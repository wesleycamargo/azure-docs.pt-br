<properties 
   pageTitle="Monitorar seu dispositivo StorSimple | Microsoft Azure"
	description="Descreve como usar o serviço StorSimple Manager para monitorar o desempenho de E/S, utilização da capacidade, taxa de transferência de rede e desempenho do dispositivo."
	services="storsimple"
	documentationCenter="NA"
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="09/02/2015"
	ms.author="alkohli"/>

# Usar o serviço StorSimple Manager para monitorar seu dispositivo StorSimple 

## Visão geral

Você pode usar o serviço StorSimple Manager para monitorar dispositivos específicos na sua solução do StorSimple. Você pode criar gráficos personalizados com base no desempenho de E/S, utilização da capacidade, taxa de transferência de rede e métricas de desempenho do dispositivo.

Para exibir as informações de monitoramento de um dispositivo específico, no Portal do Azure, selecione o serviço StorSimple Manager, clique na guia **Monitor** e, em seguida, selecione na lista de dispositivos. A página **Monitor** contém as seguintes informações.

## Desempenho de E/S 

O **desempenho de E/S** acompanha as métricas relacionadas ao número de operações de leitura e gravação entre as interfaces do iniciador iSCSI no servidor host e o dispositivo ou o dispositivo e a nuvem. Esse desempenho pode ser medido para um volume específico, um contêiner de volume específico ou todos os contêineres de volume.

A tabela abaixo mostra as E/Ss para o iniciador do dispositivo para todos os volumes de um dispositivo de produção. As métricas plotadas são bytes de leitura e gravação por segundo, operações de E/S de leitura e gravação por segundo e latências de leitura e gravação.

![Desempenho de E/S do iniciador para o dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Para o mesmo dispositivo, as E/Ss são plotadas para dados do dispositivo para a nuvem para todos os contêineres de volume. Nesse dispositivo, os dados estão apenas na camada de linear e nada vazou para a nuvem. Não há nenhuma operação de leitura/gravação ocorrendo do dispositivo para a nuvem. Como consequência, os picos no gráfico estão a um intervalo de 5 minutos, o que corresponde à frequência à qual a pulsação é verificada entre o dispositivo e o serviço.

![Desempenho de E/S do dispositivo para a nuvem](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Para o mesmo dispositivo, um instantâneo de nuvem foi capturado para dados de volume começando às 14h. Isso resultou em fluxo de dados do dispositivo para a nuvem. Leituras e gravações foram veiculadas para a nuvem essa duração. O gráfico de E/S mostra um pico em várias métricas que corresponde à hora em que o instantâneo foi capturado.

![Desempenho de E/S do dispositivo para a nuvem após o instantâneo da nuvem](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## Utilização da capacidade 

A **utilização da capacidade** acompanha as métricas relacionadas à quantidade de espaço de armazenamento de dados usada por volumes, contêineres de volume ou dispositivo. Você pode criar relatórios com base na utilização de capacidade do armazenamento principal, armazenamento em nuvem ou armazenamento do dispositivo. A utilização da capacidade pode ser medida em um volume específico, um contêiner de volume específico ou todos os contêineres de volume.

É possível descrever as capacidades de armazenamento primária, de nuvem e do dispositivo da seguinte maneira:

- A **utilização de capacidade do armazenamento principal** mostra a quantidade de dados gravados em volumes StorSimple antes de os dados passarem pela eliminação de duplicação e serem compactados. Os gráficos a seguintes mostram o uso da capacidade de armazenamento principal de um dispositivo StorSimple antes e depois de um instantâneo de nuvem ter sido capturado. Dado que são apenas dados de volume, um instantâneo de nuvem não deve alterar o armazenamento primário. Como você pode ver, o gráfico não mostra nenhuma diferença na utilização da capacidade primária como resultado de fazer um instantâneo da nuvem. Observe que o instantâneo de nuvem iniciou aproximadamente às 14h no dispositivo.

	![Utilização da capacidade primária antes do instantâneo da nuvem](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)
	
	![Utilização da capacidade primária após o instantâneo da nuvem](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)


- A **Utilização da capacidade de armazenamento em nuvem** mostra a quantidade de armazenamento em nuvem usado. Esses dados passam pela eliminação de duplicação e são compactados. O valor inclui instantâneos de nuvem que podem conter dados que não serão refletidos em nenhum volume primário e são mantidos para fins de retenção obrigatória ou herança. Você pode comparar os valores de consumo de armazenamento de nuvem e principal para obter uma ideia da taxa de redução dos dados, embora o número não vá ser exato. Os gráficos a seguintes mostram o uso da capacidade de armazenamento em nuvem de um dispositivo StorSimple antes e depois da captura de um instantâneo de nuvem. O instantâneo de nuvem iniciou aproximadamente às 14h no dispositivo e você pode ver a captura de utilização da capacidade de nuvem ao mesmo tempo, aumentando de 5,73 MB para 4,04 GB.

	![Utilização da capacidade da nuvem antes do instantâneo da nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

	![Utilização da capacidade da nuvem após o instantâneo da nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


- A **Utilização de capacidade de armazenamento do dispositivo** mostra a utilização total para o dispositivo, que será maior do que a utilização do armazenamento principal porque inclui a camada SSD linear. Essa camada contém uma quantidade de dados que também existe nas outras camadas do dispositivo. A capacidade na camada SSD linear é alternada para que quando novos dados chegam, os dados antigos sejam movidos para a camada HDD (momento no qual eles passam por eliminação de duplicação e são compactados) e subsequentemente para a nuvem.

	Ao longo do tempo, utilização da capacidade principal e utilização da capacidade do dispositivo provavelmente aumentarão juntas até que os dados comecem a ser organizados em camadas na nuvem. Nesse ponto, a utilização da capacidade do dispositivo provavelmente começará a atingir a estabilidade, mas a utilização da capacidade principal aumentará conforme mais dados forem gravados.

	Os gráficos a seguintes mostram o uso da capacidade de armazenamento principal de um dispositivo StorSimple antes e depois de um instantâneo de nuvem ter sido capturado. O instantâneo de nuvem iniciou às 14h e a utilização da capacidade do dispositivo começou a diminuir naquela hora. O uso da capacidade de armazenamento do dispositivo diminuiu de 11,58 GB para 7,48 GB. Isso indica que provavelmente os dados descompactados na camada SSD linear tiveram a duplicação eliminada e foram compactados e movidos para a camada de unidade de disco rígido. Observe que, se o dispositivo já tiver uma grande quantidade de dados nas camadas de SSD e unidade de disco rígido, você poderá não ver essa redução. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

	![Utilização da capacidade do dispositivo antes do instantâneo da nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

	![Utilização da capacidade do dispositivo após o instantâneo da nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## Taxa de transferência de rede

A **taxa de transferência de rede** acompanha as métricas relacionadas à quantidade de dados transferidos de interfaces de rede do iniciador iSCSI no servidor host e o dispositivo e entre o dispositivo e a nuvem. Você pode monitorar essa métrica para cada uma das interfaces de rede iSCSI em seu dispositivo.

Os gráficos a seguir mostram a taxa de transferência de rede para Dado 0 e Dado 4, ambas interfaces de rede de 1 GbE em seu dispositivo. Neste exemplo, Dado 0 estava habilitado para nuvem, enquanto Dado 4 estava habilitado para iSCSI. Você pode ver a entrada e a saída de tráfego para o seu dispositivo StorSimple. Observe que a linha reta no gráfico a partir das 15h24 se deve ao fato de que coletamos dados somente a cada 5 minutos e deve ser ignorada.

![Taxa de transferência de rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Taxa de transferência de rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## Desempenho do dispositivo 

O **desempenho do dispositivo** acompanha métricas relacionadas ao desempenho do seu dispositivo. O quadro a seguir mostra as estatísticas de utilização de CPU para um dispositivo em produção.

![Utilização da CPU do dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## Próximas etapas

[Aprenda a usar o painel de dispositivo do serviço StorSimple Manager](storsimple-device-dashboard.md).

<!---HONumber=September15_HO1-->