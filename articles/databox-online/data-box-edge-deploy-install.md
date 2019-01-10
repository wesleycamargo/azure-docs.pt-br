---
title: Tutorial sobre como instalar um dispositivo físico do Azure Data Box Edge | Microsoft Docs
description: O segundo tutorial sobre como instalar o Azure Data Box Edge envolve desempacotamento, montagem em rack e cabeamento do dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 6776eeb3cfdef98084c36a9441acafb8de1ab5b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720315"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Tutorial: Instalar o Azure Data Box Edge (versão prévia)

Este tutorial descreve como instalar um dispositivo físico do Data Box Edge. O procedimento de instalação envolve desempacotamento, montagem em rack e cabeamento do dispositivo. 

A instalação poderá levar cerca de duas horas para ser concluída.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Desempacotar o dispositivo
> * Montar o dispositivo em rack
> * Cabear o dispositivo

> [!IMPORTANT]
> A solução Data Box Edge está em versão prévia. Antes de solicitar e implantar essa solução, examine os [Termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos de instalação de um dispositivo físico são os seguintes:

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, verifique se:

* Você concluiu todas as etapas descritas em [Preparar a implantação do Azure Data Box Edge (versão prévia)](data-box-edge-deploy-prep.md).
    * Você criou um recurso do Data Box Edge para implantar seu dispositivo.
    * Você já gerou a chave de ativação para ativar seu dispositivo com o recurso do Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Para o dispositivo físico do Data Box Edge

Antes de implantar um dispositivo:

- Coloque o dispositivo em uma superfície de trabalho plana, estável e nivelada.
- Verifique se o local em que você pretende configurar tem:
    - Alimentação CA padrão de uma fonte independente

        -OU-
    - Uma PDU (unidade de distribuição de energia) de rack com um no-break
    - Um slot de 1U disponível no rack em que você pretende montar o dispositivo

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Examine os requisitos de rede para implantar o Data Box Edge e configure a rede de datacenter de acordo com os requisitos. Para saber mais, veja [Requisitos de rede do Data Box Edge](data-box-gateway-system-requirements.md#networking-requirements).

- Garanta que a largura de banda mínima da Internet seja de 20 Mbps para o funcionamento ideal do dispositivo.


## <a name="unpack-the-device"></a>Desempacotar o dispositivo

Este dispositivo é fornecido em uma única caixa. Conclua as etapas a seguir para desempacotar seu dispositivo. 

1. Coloque a caixa em uma superfície plana e nivelada.
2. Inspecione a caixa e a espuma da embalagem para verificar se não há amassados, cortes, danos por água ou qualquer outro dano visível. Se a caixa ou a embalagem estiver gravemente danificada, não a abra. Contate o Suporte da Microsoft para receber ajuda e saber se o dispositivo está em boas condições de funcionamento.
3. Abra a caixa. Depois de abrir a caixa, verifique se você tem estes itens:
    - Um dispositivo do Edge de compartimento único
    - Dois cabos de alimentação
    - Um kit de montagem em rack corrediço sem ferramentas (dois trilhos laterais e hardware de montagem incluídos)

Caso não tenha recebido todos os itens listados aqui, contate o suporte do Data Box Edge. A próxima etapa é montar o dispositivo em rack.


## <a name="rack-the-device"></a>Montar o dispositivo em rack

O dispositivo precisa ser instalado em um rack padrão de 19 polegadas. Use o procedimento a seguir para montar o dispositivo em um rack padrão de 19 polegadas com colunas frontal e traseira.

> [!IMPORTANT]
> Os dispositivos Data Box Edge devem ser montados em rack para a operação apropriada.


1. Puxe a liberação frontal para desbloquear o trilho interno do assembly de slides. Libere a trava de retenção e empurre o trilho intermediário para dentro para retrair o trilho.  
    Os trilhos internos e externos agora devem estar separados.

    ![Instalar o trilhas de montagem em rack](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Instale os trilhos externos nos componentes verticais do gabinete de rack. Para ajudar com a orientação, os trilhos deslizantes estão marcados com **Frente** e essa extremidade está afixada apontando para a parte dianteira do compartimento.    
    1. Localize os pinos do trilho na parte frontal e traseira do módulo do trilho. Estenda o trilho para que ele se encaixe entre as colunas do rack. Anexe o trilho externo na parte traseira do rack primeiro. Ajuste o suporte de montagem traseiro para posicioná-lo dentro dos orifícios de montagem do rack traseiro.   

    2. Empurre e segure o gatilho no suporte traseiro para expor os ganchos de metal. Alinhe o suporte traseiro e insira-o nos orifícios de montagem e, em seguida, libere o gatilho.

    3. Alinhe o colchete dianteiro com o orifício de montagem.

    4. O suporte dianteiro agora deverá estar fixado no rack. Opcionalmente, parafusos M5 x 10L podem ser usados para prender os trilhos nas colunas, se necessário. 

    ![Instalar o trilhas de montagem em rack](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Para anexar o trilho interno no chassi, verifique se as aberturas do buraco de fechadura no trilho interno estão alinhadas com os pinos de localização na lateral do chassi. As cabeças dos pinos de localização do chassi devem estar ressaltadas pelas aberturas de buraco de fechadura no trilho interno. Puxe o trilho em direção à parte frontal do chassi até que o trilho trave no lugar com clique audível. Repita com o outro trilho interno. Empurre o chassi com o trilho interno na corrediça para concluir a instalação do rack.

    ![Instalar o trilhas de montagem em rack](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Cabear o dispositivo

Os procedimentos a seguir explicam como cabear o dispositivo do Edge para energia e rede.

Antes de começar o cabeamento do dispositivo, você precisará do seguinte:

- Seu dispositivo físico do Edge, descompactado e montado no rack.
- Dois cabos de alimentação. 
- Pelo menos um cabo de rede RJ-45 de 1 GbE para conectar-se à interface de gerenciamento. Há duas interfaces de rede de 1 GbE, uma de gerenciamento e uma de dados, no dispositivo.
- Um cabo de cobre SFP+ de 25 GbE para cada interface de rede de dados a ser configurado. Pelo menos um adaptador de rede de dados da PORTA 2, PORTA 3, PORTA 4, PORTA 5 ou PORTA 6 precisa estar conectado à Internet (com conectividade com o Azure).  
- Acesso a duas unidades de distribuição de energia (recomendado).

> [!NOTE]
> - Se você estiver conectando somente um adaptador de rede de dados. recomendamos que você use um adaptador de rede de 25 GbE, como a PORTA 3, a PORTA 4, a PORTA 5 ou a PORTA 6, para enviar dados para o Azure. 
> - Para melhor desempenho e para lidar com grandes volumes de dados, considere a possibilidade de se conectar a todas as portas de dados.
> - O dispositivo do Edge deve estar conectado à rede de datacenter de modo que possa receber dados de servidores de fonte de dados. 

O dispositivo do Edge tem 8 SSDs NVMe. O painel frontal também tem botões de energia e LEDs de status. O dispositivo inclui PSUs (unidades de alimentação) redundantes na parte traseira. O dispositivo tem seis adaptadores de rede: dois adaptadores de 1 Gbps e quatro de 25 Gbps. O dispositivo tem um BMC (controlador de gerenciamento de placa base). Identifique as várias portas de dados no backplane do dispositivo.
 
  ![Backplane de um dispositivo cabeado](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Execute as etapas a seguir para cabear o dispositivo para obter energia e rede.

1. Conecte os cabos de energia a cada uma das PSUs no compartimento. Para garantir a alta disponibilidade, instale e conecte as duas PSUs a diferentes fontes de alimentação.

2. Ligue os cabos de alimentação às PDUs (unidades de distribuição de energia) do rack. Verifique se as duas PSUs usam fontes de energia separadas.

3. Conecte a PORTA 1 do adaptador de rede de 1 GbE ao computador usado para configurar o dispositivo físico. A PORTA 1 é o adaptador de gerenciamento dedicado.

4. Conecte uma ou mais das portas (PORTA 2, PORTA 3, PORTA 4, PORTA 5 ou PORTA 6) ao rede do datacenter/Internet. Se estiver se conectando na PORTA 2, use o cabo de rede RJ-45. Para as interfaces de rede de 25 GbE, use os cabos de cobre SFP+.  


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Desempacotar o dispositivo
> * Montar o dispositivo em rack
> * Cabear o dispositivo

Vá para o próximo tutorial para aprender a conectar, configurar e ativar seu dispositivo.

> [!div class="nextstepaction"]
> [Conectar e configurar o Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)


