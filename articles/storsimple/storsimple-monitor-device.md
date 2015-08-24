<properties 
   pageTitle="Usar o serviço StorSimple Manager para monitorar seu dispositivo StorSimple | Microsoft Azure"
   description="Descreve como usar o serviço StorSimple Manager para monitorar o desempenho de E/S, utilização da capacidade, taxa de transferência de rede e desempenho do dispositivo."
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
   ms.date="08/10/2015"
   ms.author="alkohli" />

# Usar o serviço StorSimple Manager para monitorar seu dispositivo StorSimple 

## Visão geral

Você pode usar o serviço StorSimple Manager para monitorar dispositivos específicos na sua solução do StorSimple. Você pode criar gráficos personalizados com base no desempenho de E/S, utilização da capacidade, taxa de transferência de rede e métricas de desempenho do dispositivo.

Para exibir as informações de monitoramento de um dispositivo específico, no Portal de Gerenciamento, selecione o serviço StorSimple Manager, clique na guia **Monitor** e, em seguida, selecione na lista de dispositivos. A página **Monitor** contém as seguintes informações.

## Desempenho de E/S 

O **desempenho de E/S** acompanha as métricas relacionadas ao número de operações de leitura e gravação entre as interfaces do iniciador iSCSI no servidor host e o dispositivo ou o dispositivo e a nuvem. Esse desempenho pode ser medido para um volume específico, um contêiner de volume específico ou todos os contêineres de volume.

## Utilização da capacidade 

A **utilização da capacidade** acompanha as métricas relacionadas à quantidade de espaço de armazenamento de dados que é usado pelo dispositivo, contêineres de volume ou volumes. Você pode criar relatórios com base na utilização de capacidade do armazenamento principal, armazenamento em nuvem ou armazenamento do dispositivo. A utilização da capacidade pode ser medida em um volume específico, um contêiner de volume específico ou todos os contêineres de volume.

- A **utilização de capacidade do armazenamento principal** mostra a quantidade de dados gravados em volumes StorSimple antes dos dados passarem pela eliminação de duplicação e serem compactados.

- A **utilização da capacidade de armazenamento em nuvem** mostra a quantidade de armazenamento em nuvem usado. Esses dados passam pela eliminação de duplicação e são compactados. O valor inclui instantâneos de nuvem que podem conter dados que não serão refletidos em nenhum volume primário e são mantidos para fins de retenção obrigatória ou herança. Você pode comparar os valores de consumo de armazenamento de nuvem e principal para obter uma ideia da taxa de redução dos dados, embora o número não vá ser exato.

- A **utilização de capacidade de armazenamento do dispositivo** mostra a utilização total para o dispositivo, que será maior do que a utilização do armazenamento principal porque ela inclui a camada SSD linear. Essa camada contém uma quantidade de dados que também existe nas outras camadas do dispositivo. A capacidade na camada SSD linear é alternada para que quando novos dados chegam, os dados antigos sejam movidos para a nuvem (momento no qual eles passam por eliminação de duplicação e são compactados).

Ao longo do tempo, utilização da capacidade principal e utilização da capacidade do dispositivo provavelmente aumentarão juntas até que os dados comecem a ser organizados em camadas na nuvem. Nesse ponto, a utilização da capacidade do dispositivo provavelmente começará a atingir a estabilidade, mas a utilização da capacidade principal aumentará conforme mais dados forem gravados.

## Taxa de transferência de rede

A **taxa de transferência de rede** acompanha as métricas relacionadas à quantidade de dados transferidos de interfaces de rede do iniciador iSCSI no servidor host e o dispositivo e entre o dispositivo e a nuvem. Você pode monitorar essa métrica para cada uma das interfaces de rede iSCSI em seu dispositivo.

## Desempenho do dispositivo 

O **desempenho do dispositivo** acompanha métricas relacionadas ao desempenho do seu dispositivo.

## Próximas etapas

[Aprenda a usar o painel de dispositivo do serviço StorSimple Manager](storsimple-device-dashboard.md).

<!---HONumber=August15_HO7-->