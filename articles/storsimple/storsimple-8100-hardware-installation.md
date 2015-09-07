<properties 
   pageTitle="Instalar o dispositivo StorSimple 8100 | Microsoft Azure"
	description="Descreve como desembalar, montar em rack e cabear do dispositivo do StorSimple 8100 antes de implantar e configurar o software."
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
	ms.date="08/26/2015"
	ms.author="alkohli"/>

# Desembalar, montar em rack e cabear o dispositivo StorSimple 8100.

## Visão geral

Seu Microsoft Azure StorSimple 8100 é um dispositivo único de compartimento montado em rack.

Este tutorial explica como desempacotar, montar em rack e cabear o hardware do dispositivo StorSimple 8100 para que seja possível configurar o software StorSimple.

## Desempacotar o dispositivo StorSimple 8100

As etapas a seguir fornecem instruções claras e detalhadas sobre como desempacotar seu dispositivo de armazenamento StorSimple 8100. Este dispositivo é fornecido em uma única caixa.

### Preparar para desempacotar o dispositivo

Antes de desempacotar o dispositivo, leia as informações a seguir.

>[AZURE.WARNING]![ícone de peso pesado](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)
>
> 1. Se for manejar o dispositivo manualmente, assegure-se de que terá a ajuda de duas pessoas que possam aguentar o peso do dispositivo. Um dispositivo completamente configurado pode pesar até 32 kg (70 lbs.).
>
> 2. Coloque a caixa em uma superfície plana e nivelada.

Conclua as etapas a seguir para desempacotar o dispositivo.

#### Para desempacotar o dispositivo

1. Inspecione a caixa e a espuma da embalagem para verificar se não há amassados, cortes, danos por água ou qualquer outro dano visível. Se a caixa ou a embalagem estiver muito danificada, não a abra. Contate o Suporte da Microsoft para receber ajuda e saber se o dispositivo está em boas condições de funcionamento. 

2. Abra a caixa. A imagem a seguir mostra o dispositivo Azure StorSimple desempacotado.

     ![Desempacotar o dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Exibição do dispositivo de armazenamento desempacotado**

     Rótulo | Descrição 
     ----- | -------------
     1 | Embalagem
     2 | Espuma inferior
     3 | Dispositivo
     4 | Espuma superior
     5 | Caixa de acessório

3. Depois de abrir a caixa, verifique se você tem estes itens:

   - 1 único dispositivo de compartimento
   - 2 cabos de energia
   - 1 cabo cruzado Ethernet
   - 2 cabos seriais de console
   - 1 conversor serial USB para acesso serial
   - 1 chave de fenda T10 à prova de violação
   - 4 adaptadores exclusivos QSFP para SFP+
   - 1 kit de montagem em rack (2 trilhos laterais com hardware de montagem)
   - Documentação de Introdução

    Se você não recebeu algum dos itens listados acima, contate o Suporte da Microsoft.

A próxima etapa é montar o dispositivo em rack.

## Montar em rack o dispositivo StorSimple 8100

Siga as próximas etapas para instalar o dispositivo de armazenamento StorSimple 8100 em um rack padrão de 19 polegadas (48 cm) com colunas frontal e traseira. O dispositivo StorSimple 8100 tem um único compartimento principal.

A instalação consiste em várias etapas, e cada uma delas é discutida nos procedimentos a seguir.

### Preparar o local

O dispositivo deve ser instalado em um rack padrão de 19 polegadas (48 cm) com colunas frontal e traseira. Use o procedimento a seguir para preparar a instalação do rack.

#### Para preparar o local para instalação do rack

1. Certifique-se de colocar o dispositivo em uma superfície de trabalho plana, estável e nivelada (ou em local semelhante) de modo que ele fique seguro.

2. Verifique se o local onde você pretende instalar tem energia de corrente alternada padrão de uma fonte independente ou uma PDU (unidade de distribuição de energia) em rack com um no-break.

3. Verifique se um slot de 2U está disponível no rack em que você pretende montar o dispositivo.

>[AZURE.WARNING]![ícone de peso pesado](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)
> 
> Se for instalar o dispositivo manualmente, assegure-se de que terá a ajuda de duas pessoas que possam aguentar o peso do dispositivo. Um compartimento totalmente configurado pode pesar até 32 kg (70 lbs.).

### Pré-requisitos do rack

O compartimento 8100 foi projetado para instalação em um gabinete padrão de 19 polegadas com:

- Profundidade mínima de 27,84 polegadas de uma coluna a outra do rack.
- Peso máximo de 32 kg para o dispositivo
- Pressão de retorno máxima de 5 Pascal (medidor de água de 0,5 mm).

### Kit do trilho de montagem em rack

Um conjunto de trilhos de montagem é fornecido para ser usado com o gabinete de rack de 19 polegadas. Os trilhos foram testados para suportar o peso máximo do compartimento. Esses trilhos também permitirão a instalação de vários compartimentos sem nenhuma perda de espaço dentro do rack.

#### Para instalar o dispositivo nos trilhos

1. Com o compartimento na superfície de trabalho, remova as tampas do flange frontal direito e esquerdo, soltando-as. As tampas do flange estão apenas encaixadas nos flanges.

2. Normalmente, os trilhos são instalados na fábrica. Caso contrário, instale as corrediças do trilho esquerdo e direito nas laterais dos chassis do compartimento. Elas são presas por seis parafusos métricos em cada lado. Para ajudar com a orientação, as corrediças do trilho são marcadas com **LH – Frontal** e **RH – Frontal**, e a extremidade que é afixada na parte traseira do compartimento é afunilada.<br/>

    ![Fixando as corrediças do trilho nos chassis do compartimento](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

   **Fixando corrediças do trilho às laterais do compartimento**

    Label | Description
    ----- | -----------
    1     | M 3x4 button-head screws
    2     | Chassis slides

3. Fixe os módulos dos trilhos direito e esquerdo nos membros verticais do gabinete do rack. Os suportes são marcados com **LH**, **RH** e **Este lado para cima** para que você obtenha a orientação correta.

4. Localize os pinos do trilho na parte frontal e traseira do módulo do trilho. Estenda o trilho para que ele se encaixe entre as colunas do rack e insira os pinos nos orifícios do membro vertical da coluna do rack frontal e traseiro. Certifique-se de que o módulo do trilho esteja nivelado.

5. Use dois dos parafusos métricos fornecidos para prender o módulo do trilho nos membros verticais do rack. Use um parafuso na parte frontal e outro na parte traseira.

6. Repita estas etapas para o outro módulo do trilho.<br/>

     ![Fixando as corrediças do trilho no gabinete do rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Fixando os conjuntos de trilhos ao rack**

     Rótulo | Descrição
     ----- | -----------
     1 | Parafuso de travamento
     2 | Parafuso da coluna do rack frontal para orifício quadrado
     3 | Pinos de localização do trilho frontal esquerdo
     4 | Parafuso de travamento
     5 | Pinos de localização do trilho frontal direito

### Montando o dispositivo no rack

Usando os trilhos do rack que acabaram de ser instalados, execute as etapas a seguir para montar o dispositivo no rack.

#### Para montar o dispositivo

1. Com um assistente, levante o compartimento e alinhe-o com os trilhos do rack. 

2. Com cuidado, insira o dispositivo nos trilhos e empurre-o completamente para dentro do gabinete do rack.<br/>

    ![Inserindo o dispositivo no rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montando o dispositivo no rack**

3. Fixe o compartimento no rack usando um parafuso Phillips fornecido em cada flange, esquerdo e direito.

4. Instale as tampas do flange pressionando-as na posição e encaixando-as no lugar.<br/>

     ![Instalando as tampas do flange](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
 
    **Instalando as tampas do flange**

     Rótulo | Descrição
     ----- | -----------
     1 | Parafuso de fixação do compartimento

A próxima etapa é fazer o cabeamento do dispositivo para recebimento de energia, acesso à rede e acesso serial.

## Cabear o dispositivo StorSimple 8100

Os procedimentos a seguir explicam como cabear o dispositivo StorSimple 8100 para conexões de energia, rede e serial.

### Pré-requisitos

Antes de começar o cabeamento do dispositivo, você precisará dos seguintes itens:

- Dispositivo de armazenamento totalmente desempacotado

- 2 cabos de energia que acompanham o dispositivo

- Acesso a 2 unidades de distribuição de energia (recomendado).

- Cabos de rede

- Cabos seriais fornecidos

- Conversor USB serial com o driver apropriado instalado em seu computador (se necessário)

- Adaptadores exclusivos QSFP para SFP+ fornecidos para serem usados com interfaces de rede de 10 GbE

- [Suporte para hardware de interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### Cabeamento de energia

Seu dispositivo inclui PCMs (Módulos de Energia e Refrigeração) redundantes. Ambos os PCMs devem ser instalados e conectados a diferentes fontes de energia para garantia de alta disponibilidade.

Execute as etapas a seguir para cabear o dispositivo para recebimento de energia.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### Cabeamento de rede

O dispositivo é uma configuração ativa/em espera: em qualquer determinado momento, um módulo do controlador está ativo e processando todas as operações de disco e rede, enquanto o outro módulo do controlador está em espera. Se o controlador falhar, o controlador em espera será ativado imediatamente e dará continuidade a todas as operações de disco e rede.

Para oferecer suporte a este failover de controlador redundante, você precisa cabear a rede do dispositivo, conforme descrito nas etapas a seguir.

#### Para cabear a conexão de rede

1. Seu dispositivo tem seis interfaces de rede em cada controlador: quatro de 1 Gbps e duas portas Ethernet de 10 Gbps. Identifique as várias portas de dados no backplane do dispositivo.

    ![Backplane do dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Parte traseira do dispositivo mostrando as portas de dados**
 
     Rótulo | Descrição
     ------- | -----------
     0,1,4,5 | Interfaces de rede de 1 GbE
     2,3 | Interfaces de rede de 10 GbE
     6 | Portas seriais

2. Para alta disponibilidade, o dispositivo exige um mínimo de duas conexões para cada controlador.
    1. A porta DATA 0 é habilitada e configurada automaticamente por meio do console serial do dispositivo. Além da DATA 0, outra porta de dados também precisa ser configurada por meio do Portal de Gerenciamento. 
    2. Identifique as interfaces de rede idênticas em cada controlador. Por exemplo, se você optar por conectar DATA 0 e DATA 3 em um dos controladores, precisará conectar DATA 0 e DATA 3 correspondentes no outro controlador. 

3. Para alta disponibilidade, certifique-se de conectar:
    1. Interfaces idênticas em cada controlador à rede relevante para garantir disponibilidade caso ocorra uma falha do controlador.
    2. Interfaces de cada controlador para pelo menos dois comutadores diferentes para garantir disponibilidade caso ocorra uma falha do comutador.
    3. Porta DATA 0 à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser conectadas ao segmento SAN/iSCSI LAN (VLAN) da rede, dependendo da função pretendida.

    No mínimo, configure uma interface de rede para acesso à nuvem e uma para iSCSI. Para alta disponibilidade e desempenho, configure dois pares de interfaces de rede em cada controlador. Consulte o diagrama a seguir para cabeamento de rede. (A configuração mínima de rede é mostrada pelas linhas azuis sólidas. Para alta disponibilidade e desempenho, a configuração adicional necessária é mostrada pelas linhas pontilhadas).

    ![Cabear o dispositivo 2U para rede](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cabeamento de rede para o dispositivo**

    Rótulo | Descrição
    ----- | -----------
     Uma | LAN com acesso à Internet
     B | Controlador 0
     C | PCM 0
     D | Controlador 1
     E | PCM 1
     F,G | Hosts
     0-5 | Interfaces de rede
   
### Cabeamento de porta serial

Execute as etapas a seguir para cabear a porta serial.

#### Para cabear a conexão serial

1. O dispositivo tem uma porta serial em cada controlador que é identificada por um ícone de chave inglesa. Consulte a ilustração na seção [Cabeamento de rede](#network-cabling) para localizar as portas seriais no backplane do dispositivo. 

2. Identifique o controlador ativo no backplane do dispositivo. Um LED azul piscante indica que o controlador está ativo.

3. Use os cabos seriais fornecidos (se necessário, o conversor serial USB para seu laptop) e conecte o console ou o computador (com emulação de terminal para o dispositivo) com a porta serial do controlador ativo.

4. Instale os drivers de USB seriais (fornecidos com o dispositivo) em seu computador.

5. Configure a conexão serial da seguinte maneira: 115.200 bauds, 8 bits de dados, 1 bit de parada, sem paridade e controle de fluxo definida como Nenhum.

6. Verifique se a conexão está funcionando pressionando Enter no console. Um menu de console serial deve aparecer.

>[AZURE.NOTE]**Gerenciamento Noturno**: quando o dispositivo for instalado em um data center remoto ou em uma sala de computadores com acesso limitado, certifique-se de que as conexões seriais com ambos os controladores sempre estejam conectadas a um comutador de console serial ou equipamento semelhante. Isso permite operações de suporte e controle remoto fora de banda se houver interrupções na rede ou falhas inesperadas.

Agora o dispositivo está cabeado para energia, acesso à rede e conectividade serial. A próxima etapa é configurar o software no dispositivo.

## Próximas etapas

Agora você está pronto para [implantar e configurar seu dispositivo StorSimple no local](storsimple-deployment-walkthrough.md)
 

<!---HONumber=August15_HO9-->