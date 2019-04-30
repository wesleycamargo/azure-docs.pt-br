---
title: Pacote de Borda de importação de solução de Monitoramento Remoto - Azure|Microsoft Docs
description: Este artigo descreve como importar um pacote do IoT Edge para o acelerador de solução de monitoramento remoto
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/10/2018
ms.date: 12/17/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442829"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importe um pacote IoT Edge para o acelerador de solução de monitoramento remoto

Um manifesto de implantação define os módulos para implantar em um dispositivo IoT Edge. Este artigo presume que um desenvolvedor em sua organização já criou o manifesto de implantação. Para saber como um desenvolvedor cria um manifesto, consulte um dos seguintes artigos de instruções do IoT Edge:

- [Implante os módulos do Azure IoT Edge no portal do Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Implante os módulos do Azure IoT Edge com a CLI do Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Implantar módulos do Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Um desenvolvedor cria e testa um manifesto de implantação em um ambiente de desenvolvimento. Quando estiver pronto, você poderá importar o manifesto para o acelerador de solução de monitoramento remoto.

## <a name="export-a-manifest"></a>Exportar um manifesto

Use o portal do Azure para exportar o manifesto de implantação do seu ambiente de desenvolvimento:

1. No portal do Azure, navegue até o hub IoT que você está usando para desenvolver e testar seus dispositivos IoT Edge. Clique em **do IoT Edge** e, em seguida **implantações do IoT Edge**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Clique na implantação que tem a configuração de implantação que você deseja usar. O **detalhes da implantação** página exibe: ![Detalhes da implantação do IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Clique em **manifesto do IoT Edge baixar**:  ![Baixe o manifesto de implantação](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Salve o arquivo JSON como um arquivo local chamado **deploymentmanifest.json**.

Agora você tem um arquivo que contém o manifesto de implantação. Na próxima seção, você importa esse manifesto como um pacote para a solução de monitoramento remoto.

## <a name="import-a-package"></a>Importar um pacote

Siga as etapas abaixo para importar um manifesto de implantação do Edge como um pacote para sua solução:

1. Navegue até a página **Pacotes** na interface do usuário da Web do Monitoramento Remoto:  ![Página de pacotes](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Clique em **+ novo pacote**, escolha **borda manifesto** como o tipo de pacote e clique em **procurar** para selecionar o **deploymentmanifest.json** arquivo você salvou na seção anterior:  ![Selecione o manifesto](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Clique em **Carregar** para adicionar o pacote à sua solução de Monitoramento Remoto:  ![Pacote carregado](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Agora você carregou um manifesto de implantação do IoT Edge como um pacote. Na página **Implantações**, você pode implantar esse pacote em seus dispositivos IoT Edge conectados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a implantar módulos em um dispositivo IoT Edge a partir da solução de monitoramento remoto, a próxima etapa é aprender mais sobre [IoT Edge](../iot-edge/about-iot-edge.md).
