---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "58115981"
---
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol)  

Antes de usar a conta FTP em um aplicativo lógico, você deve autorizá-lo a se conectar à sua conta FTP. Felizmente, é possível fazer isso facilmente dentro do seu aplicativo lógico no Portal do Azure.  

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta FTP:  

1. Para criar uma conexão com o FTP, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e digite *FTP* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar:   
   ![Etapa de criação de conexão com o FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Se você não tiver criado nenhuma conexão com o FTP antes, suas credenciais do FTP serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar e usar os dados da sua conta FTP:  
   ![etapa de criação de conexão com o FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas em seu aplicativo lógico:  
   ![etapa de criação de conexão com o FTP](./media/connectors-create-api-ftp/ftp-3.png)  

