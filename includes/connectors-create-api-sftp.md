---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d249a205c64f4e067f2d81c7e1068c8ad9756958
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "58115548"
---
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  

Antes de usar sua conta do SFTP em um aplicativo lógico, você deve autorizá-lo a se conectar à conta SFTP. Felizmente, você pode fazer isso de forma fácil usando seu aplicativo lógico no Portal do Azure.  

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à conta SFTP:  

1. Para criar uma conexão com o SFTP, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e digite *SFTP* na caixa de pesquisa. Selecione o gatilho **SFTP - Quando um arquivo é adicionado ou modificado**:  
   ![Imagem de conexão online de SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Se você não tiver criado nenhuma conexão com o SFTP antes, suas credenciais do SFTP serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar e acessar os dados da sua conta SFTP:  
   ![Imagem de conexão online de SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas em seu aplicativo lógico:   
   ![Imagem de conexão online de SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 

