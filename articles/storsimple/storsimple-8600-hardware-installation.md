<properties 
   pageTitle="Instalar o dispositivo StorSimple 8600 | Microsoft Azure"
   description="Descreve como desembalar, montar em rack e cabear do dispositivo do StorSimple 8600 antes de implantar e configurar o software."
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
   ms.date="09/16/2015"
   ms.author="alkohli" />

# Desembalar, montar em rack e cabear o dispositivo StorSimple 8600.

## Visão geral
Seu Microsoft Azure StorSimple 8600 é um dispositivo de compartimento duplo e consiste em um compartimento principal e um EBOD. Este tutorial explica como desempacotar, montar em rack e cabear o hardware do dispositivo StorSimple 8600 para que seja possível configurar o software StorSimple.

## Desempacotar o dispositivo StorSimple 8600

As etapas a seguir fornecem instruções claras e detalhadas sobre como desempacotar seu dispositivo de armazenamento StorSimple 8600. Este dispositivo é fornecido em duas caixas separadas, uma para o compartimento principal e outra para o compartimento EBOD.

### Preparar para desempacotar o dispositivo

Antes de desempacotar o dispositivo, leia as informações a seguir.

>[AZURE.WARNING]![ícone de peso pesado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
>
> 1. Se for manejar o dispositivo manualmente, assegure-se de que terá a ajuda de duas pessoas que possam aguentar o peso do dispositivo. Um dispositivo completamente configurado pode pesar até 32 kg (70 lbs.).
>
> 2. Coloque a caixa em uma superfície plana e nivelada.

Conclua as etapas a seguir para desempacotar o dispositivo.

#### Para desempacotar o dispositivo

1. Inspecione as caixas e a espuma da embalagem para verificar se não há amassados, cortes, danos por água ou qualquer outro dano visível. Se a caixa ou a embalagem estiver muito danificada, não a abra. Contate o Suporte da Microsoft para receber ajuda e saber se o dispositivo está em boas condições de funcionamento.

2. Abra as duas caixas e desempacote os compartimentos principal e EBOD. A figura a seguir mostra o dispositivo de armazenamento desempacotado.

    ![Desempacotar o dispositivo de armazenamento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
 
    **Exibição do dispositivo de armazenamento desempacotado**

     Rótulo | Descrição 
     ----- | -------------
     1 | Embalagem
     2 | Cabos SAS (na bandeja de acessórios e cabos)
     3 | Espuma inferior
     4 | Dispositivo
     5 | Espuma superior
     6 | Caixa de acessório

3. Depois de abrir as duas caixas, verifique se você tem estes itens:

  - 1 compartimento principal (o compartimento principal e o compartimento EBOD estão em duas caixas separadas) 
  - 1 compartimento EBOD
  - 4 cabos de alimentação, 2 em cada caixa
  - 2 cabos SAS (para conectar o compartimento principal ao compartimento EBOD)
  - 1 cabo cruzado Ethernet
  - 2 cabos seriais de console
  - 1 conversor serial USB para acesso serial
  - 4 adaptadores exclusivos QSFP para SFP+ para serem usados com interfaces de rede de 10 GbE
  - 2 kits de montagem em rack (4 trilhos laterais com hardware de montagem, 2 para o compartimento principal e 2 para o compartimento EBOD), 1 em cada caixa
  - Documentação de Introdução

    Se você não recebeu algum dos itens listados acima, contate o Suporte da Microsoft.

A próxima etapa é montar o dispositivo em rack.

## Montar em rack o dispositivo StorSimple 8600

Siga as próximas etapas para instalar o dispositivo de armazenamento StorSimple 8600 em um rack padrão de 19 polegadas (48 cm) com colunas frontal e traseira. Esse dispositivo é fornecido com dois compartimentos: um principal e um EBOD. Ambos precisam ser montados em rack.

A instalação consiste em várias etapas, e cada uma delas é discutida nos procedimentos a seguir.

### Preparação do local

Os compartimentos devem ser instalados em um rack padrão de 19 polegadas (48 cm) com colunas frontal e traseira. Use o procedimento a seguir para preparar a instalação do rack.

#### Para preparar o local para instalação do rack

1. Certifique-se de colocar os compartimentos principal e EBOD em uma superfície de trabalho plana, estável e nivelada (ou em local semelhante) de modo que fiquem seguros.

2. Verifique se o local onde você pretende instalar tem energia de corrente alternada padrão de uma fonte independente ou uma PDU (Unidade de Distribuição de Energia) em rack com um no-break.

3. Verifique se um slot de 4U (2 X 2U) está disponível no rack em que você pretende montar os compartimentos.

>[AZURE.WARNING]![ícone de peso pesado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
> 
> Se for instalar o dispositivo manualmente, assegure-se de que terá a ajuda de duas pessoas que possam aguentar o peso do dispositivo. Um compartimento totalmente configurado pode pesar até 32 kg (70 lbs.).

### Pré-requisitos do rack

Os compartimentos foram projetados para instalação em um gabinete de rack padrão de 19 polegadas (48 cm) com:

- Profundidade mínima de 27,84 polegadas (71 cm) de uma coluna a outra do rack
- Peso máximo de 32 kg para o dispositivo
- Pressão de retorno máxima de 5 Pascal (medidor de água de 0,5 mm)

### Kit do trilho de montagem em rack

Um conjunto de trilhos de montagem será fornecido para ser usado com o gabinete de rack de 19 polegadas. Os trilhos foram testados para suportar o peso máximo do compartimento. Esses trilhos também permitirão a instalação de vários compartimentos sem perda de espaço dentro do rack. Instale primeiro o compartimento EBOD.

#### Para instalar o compartimento EBOD nos trilhos

1. Com o compartimento na superfície de trabalho, remova as tampas do flange frontal direito e esquerdo, soltando-as. As tampas do flange estão apenas encaixadas nos flanges.

2. Normalmente, esses trilhos são instalados na fábrica. Caso contrário, instale as corrediças do trilho esquerdo e direito nas laterais dos chassis do compartimento. Elas são presas por seis parafusos métricos em cada lado. Para ajudar com a orientação, as corrediças do trilho são marcadas com **LH – Frontal** e **RH – Frontal**, e a extremidade que é afixada na parte traseira do compartimento é afunilada.

    ![Fixando as corrediças do trilho nos chassis do compartimento](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Fixando corrediças do trilho às laterais do compartimento**

    Rótulo | Descrição
    ----- | -----------
    1 | Parafusos de cabeça abaulada M 3x4
    2 | Corrediças dos chassis
 
3. Fixe os módulos dos trilhos direito e esquerdo nos membros verticais do gabinete do rack. Os suportes são marcados com **LH**, **RH** e **Este lado para cima** para que você obtenha a orientação correta.

4. Localize os pinos do trilho na parte frontal e traseira do módulo do trilho. Estenda o trilho para que ele se encaixe entre as colunas do rack e insira os pinos nos orifícios do membro vertical da coluna do rack frontal e traseiro. Certifique-se de que o módulo do trilho esteja nivelado.

5. Prenda o módulo do trilho nos membros verticais do rack usando dois dos parafusos métricos fornecidos. Use um parafuso na parte frontal e outro na parte traseira.

6. Repita estas etapas para o outro módulo do trilho.

     ![Fixando as corrediças do trilho no gabinete do rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Fixando os conjuntos de trilhos ao rack**

     Rótulo | Descrição
     ----- | -----------
     1 | Parafuso de travamento
     2 | Parafuso da coluna do rack frontal para orifício quadrado
     3 | Pinos de localização do trilho frontal esquerdo
     4 | Parafuso de travamento
     5 | Pinos de localização do trilho frontal direito

### Montando o compartimento EBOD no rack 

Usando os trilhos do rack que acabaram de ser instalados, execute as etapas a seguir para montar o compartimento EBOD no rack.

#### Para montar o compartimento EBOD

1. Com um assistente, levante o compartimento e alinhe-o com os trilhos do rack. 

2. Com cuidado, insira o compartimento nos trilhos e empurre-o completamente para dentro do gabinete do rack.

    ![Inserindo o dispositivo no rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montando o compartimento no rack**

3. Prenda o compartimento no rack usando um parafuso Phillips fornecido em cada flange, esquerdo e direito.

4. Instale as tampas do flange pressionando-as na posição e encaixando-as no lugar.

     ![Instalando as tampas do flange](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Instalando as tampas do flange**
 
     Rótulo | Descrição
     ----- | -----------
     1 | Parafuso de fixação do compartimento


### Montando o compartimento principal no rack

Depois de acabar de montar o compartimento EBOD, você precisará montar o compartimento principal seguindo as mesmas etapas.

> [AZURE.NOTE]
> 
> - É possível ter alguns slots vazios no rack entre os compartimentos principal e EBOD. 
> - Use o cabo SAS de 2 m fornecido para conectar o compartimento principal ao compartimento EBOD.
> - Não há restrições quanto ao posicionamento relativo da unidade principal com a unidade EBOD. Portanto, o compartimento principal pode ser colocado no slot superior e o compartimento EBOD abaixo, ou vice-versa.

A próxima etapa é fazer o cabeamento do dispositivo para recebimento de energia, acesso à rede e acesso serial.

## Cabear o dispositivo StorSimple 8600

Os procedimentos a seguir explicam como cabear o dispositivo StorSimple 8600 para conexões de energia, rede e serial.

### Pré-requisitos

Antes de começar a cabear o dispositivo, você precisará dos seguintes itens:

- O compartimento principal e o compartimento EBOD completamente desempacotados
- 4 cabos de energia (2 para cada compartimento) que acompanham o dispositivo
- 2 cabos SAS fornecidos com o dispositivo para conectar o compartimento EBOD ao compartimento principal
- Acesso a 2 PDUs (recomendado)
- Cabos de rede
- Cabos seriais fornecidos
- Conversor USB-serial com o driver apropriado instalado em seu computador (se necessário)
- Adaptadores exclusivos QSFP para SFP+ fornecidos para serem usados com interfaces de rede de 10 GbE
- [Suporte para hardware de interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md) 

### Cabeamento de energia e SAS

O dispositivo tem um compartimento principal e um compartimento EBOD. Isso requer que as unidades sejam conectadas juntas para a conectividade e energia do SAS (Serial Attached SCSI).

Ao configurar este dispositivo pela primeira vez, execute as etapas para o cabeamento SAS primeiro e, em seguida, conclua as etapas do cabeamento de energia.

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### Cabeamento de rede

O dispositivo está em uma configuração ativa/em espera: em qualquer dado momento, um módulo do controlador está ativo e processando todas as operações de disco e rede, enquanto o outro módulo do controlador está em espera. Se ocorrer uma falha de controlador, o controlador em espera será imediatamente ativado e dará continuidade a todas as operações de disco e de rede.

Para oferecer suporte a este failover de controlador redundante, você precisa cabear a rede do dispositivo, conforme mostrado nas etapas a seguir.

#### Para cabear a conexão de rede

1. Seu dispositivo tem seis interfaces de rede em cada controlador: quatro de 1 Gbps e duas portas Ethernet de 10 Gbps. Consulte a ilustração a seguir para identificar as portas de dados no backplane do dispositivo.

     ![Backplane do dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Parte de trás do dispositivo mostrando as portas de dados**
 
     Rótulo | Descrição
     ------- | -----------
     0,1,4,5 | Interfaces de rede de 1 GbE
     2,3 | Interfaces de rede de 10 GbE
     6 | Portas seriais

2. Para alta disponibilidade, o dispositivo exige um mínimo de duas conexões para cada controlador.
    1. A porta DATA 0 é habilitada e configurada automaticamente por meio do console serial do dispositivo. Além da DATA 0, outra porta de dados também precisa ser configurada por meio do Portal de Gerenciamento.
    2. Identifique as interfaces de rede idênticas em cada controlador. Por exemplo, se você optar por conectar DATA 0 e DATA 3 em um dos controladores, precisará conectar DATA 0 e DATA 3 correspondentes no outro controlador. 

3. Para alta disponibilidade, certifique-se de conectar:
   1. Interfaces idênticas em cada controlador à rede relevante para garantir disponibilidade em uma falha do controlador.
   2. Interfaces de cada controlador a pelo menos dois comutadores diferentes para garantir disponibilidade em uma falha do comutador.
   3. Porta DATA 0 à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser conectadas ao segmento SAN/iSCSI LAN (VLAN) da rede, dependendo da função pretendida.

No mínimo, configure uma interface de rede para acesso à nuvem e uma para iSCSI. Para alta disponibilidade e desempenho, configure dois pares de interfaces de rede em cada controlador. Consulte o diagrama a seguir para cabeamento de rede. (A configuração mínima de rede é mostrada pelas linhas azuis sólidas. Para alta disponibilidade e desempenho, a configuração adicional necessária é mostrada pelas linhas pontilhadas).

![Cabear o dispositivo 4U para rede](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cabeamento de rede para o dispositivo**

Rótulo | Descrição
----- | -----------
Uma | LAN com acesso à Internet
B | Controlador 0
C | PCM 0
D | Controlador 1
E | PCM 1
F | Controlador 0 do EBOD
G | Controlador 1 do EBOD
H,I | Hosts (por exemplo, servidores de arquivos)
0-5 | Interfaces de rede
6 | Compartimento principal
7 | Compartimento EBOD

### Cabeamento de porta serial

Execute as etapas a seguir para cabear a porta serial.

#### Para cabear a conexão serial

1. O dispositivo tem uma porta serial em cada controlador que é identificada por um ícone de chave inglesa. Para localizar as portas seriais, consulte a ilustração que mostra as portas de dados na parte traseira do dispositivo.

2. Identifique o controlador ativo no backplane do dispositivo. Um LED azul piscante indica que o controlador está ativo.

3. Use o cabo serial fornecido (se necessário, o conversor serial USB para seu laptop) e conecte o console ou o computador (com emulação de terminal para o dispositivo) com a porta serial do controlador ativo.

4. Instale os drivers de USB seriais (fornecidos com o dispositivo) em seu computador.

5. Configure a conexão serial como se segue:
   - 115\.200 bauds
   - 8 bits de dados
   - 1 bit de parada
   - Sem paridade
   - Controle de fluxo definido como **Nenhum**

6. Verifique se a conexão está funcionando pressionando Enter no console. Um menu de console serial deve aparecer.

> [AZURE.NOTE]**Gerenciamento Noturno:** quando o dispositivo for instalado em um data center remoto ou em uma sala de computadores com acesso limitado, certifique-se de que as conexões seriais com ambos os controladores sempre estejam conectadas a um comutador de console serial ou equipamento semelhante. Isso permite operações de suporte e controle remoto fora de banda em caso de interrupção na rede ou falhas inesperadas.

Você concluiu o cabeamento do dispositivo para conexão serial, de energia e acesso à rede. A próxima etapa é configurar o software no dispositivo.

## Próximas etapas

Agora você está pronto para [implantar e configurar seu dispositivo StorSimple no local](storsimple-deployment-walkthrough.md)
 

<!---HONumber=Sept15_HO3-->