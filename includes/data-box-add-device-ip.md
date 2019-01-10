---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550213"
---
1. Entre no dispositivo Data Box. Verifique se ele está desbloqueado.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Acesse **Definir adaptadores de rede**. Anote o endereço IP do dispositivo para o adaptador de rede usado para se conectar ao cliente.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Acesse **Conectar e copiar** e clique em **REST (Versão Prévia)**.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Na caixa de diálogo **Acessar a conta de armazenamento e carregar dados**, copie o **Ponto de Extremidade de Serviço Blob**.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Inicie o **Bloco de notas** como administrador e, em seguida, abra o arquivo **hosts** localizado em `C:\Windows\System32\Drivers\etc`.
6. Adicione a seguinte entrada ao arquivo **hosts**: `<device IP address> <Blob service endpoint>`
7. Para referência, use a imagem a seguir. Salve o arquivo **hosts**.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
