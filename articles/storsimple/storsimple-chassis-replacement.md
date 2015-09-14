<properties 
   pageTitle="Substituir o chassi em um dispositivo StorSimple | Microsoft Azure"
	description="Descreve como remover e substituir o chassi em seu dispositivo StorSimple primário ou compartimento EBOD."
	services="storsimple"
	documentationCenter=""
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/31/2015"
	ms.author="alkohli"/>

# Substituir o chassi em seu dispositivo StorSimple

## Visão geral

Este tutorial explica como remover e substituir um chassi em um dispositivo StorSimple. O modelo StorSimple 8100 é um dispositivo de compartimento único (um chassi), enquanto o 8600 é um dispositivo de compartimento duplo (dois chassis). No modelo 8600, existem potencialmente dois chassis que podem falhar no dispositivo: o chassi do compartimento primário ou o chassi do compartimento EBOD.

Em ambos os casos, o chassi de substituição enviado pela Microsoft estará vazio. Não será incluído nenhum módulos de energia e resfriamento (PCM), módulo de controlador, unidade de disco de estado sólido (SSD), unidade de disco rígido (HD) ou módulos EBOD.

>[AZURE.IMPORTANT]Antes de remover e substituir p chassi, examine as informações de segurança em [Substituição de componentes de hardware do StorSimple](storsimple-hardware-component-replacement.md).

## Remover o chassi

Execute as seguintes etapas para remover o chassi em seu dispositivo StorSimple.

#### Para remover um chassi

1. Certifique-se de que o dispositivo StorSimple esteja desligado e desconectado de todas as fontes de alimentação.

2. Remova todos os cabos SAS e de rede, se houver.

3. Remova a unidade do rack.

4. Remova cada uma das unidades e observe os slots do quais são removidas. Para obter mais informações, consulte [Remover a unidade de disco](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. No compartimento EBOD (se for o chassi com falha), remova os módulos do controlador EBOD. Para obter mais informações, consulte [Remover um controlador EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller).

    No compartimento primário (se for o chassi com falha), remova os controladores e observe os slots dos quais são removidos. Para obter mais informações, consulte [Remover um controlador](storsimple-controller-replacement.md#remove-a-controller).

## Instalar o chassi

Execute as seguintes etapas para instalar o chassi em um dispositivo Microsoft Azure StorSimple.

#### Para instalar um chassi

1. Monte o chassi no rack. Para obter mais informações, consulte [Montar o dispositivo StorSimple 8100 em rack](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [Montar o dispositivo StorSimple 8600 em rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Depois que o chassis estiver montado no rack, instale os módulos do controlador nas mesmas posições que eles estavam instalados anteriormente.

3. Instale as unidades nas mesmas posições e slots de que elas estavam instaladas anteriormente.

    >[AZURE.NOTE]Em geral, é recomendável colocar primeiro os SSDs nos slots e, em seguida, instalar os HDs.

2. Com o dispositivo montado no rack e os componentes instalados, conecte o dispositivo nas fontes de alimentação apropriadas e ligue o dispositivo. Para obter detalhes, consulte [Cabeamento do dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [Cabeamento do dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## Próximas etapas

Saiba mais sobre a [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md)

<!---HONumber=September15_HO1-->