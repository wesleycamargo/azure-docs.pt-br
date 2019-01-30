---
title: Transformar dados com o Azure Data Box Edge | Microsoft Docs
description: Saiba como configurar a função de computação no Data Box Edge e usá-la para transformar dados antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c52c311f1e1cd1335ea5797eadacd0bc89e1b36c
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402108"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Tutorial: Transformar dados com o Azure Data Box Edge (versão prévia)

Este tutorial descreve como configurar uma função de computação no dispositivo do Azure Data Box Edge. Depois de configurar a função de computação, o Data Box Edge pode transformar os dados antes de enviar para o Azure.

Esse procedimento leva cerca de 30 a 45 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um recurso do Hub IoT do Azure
> * Configurar a função de computação
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Antes de pedir e implantar essa solução, examine os [Termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo do Data Box Edge, certifique-se de que:

* Você ativou o dispositivo do Data Box Edge conforme descrito em [Conectar, configurar e ativar o Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Criar um recurso do Hub IoT

Antes de configurar uma função de computação no Data Box Edge, você deve criar um recurso de Hub IoT.

Para obter instruções detalhadas, acesse [Criar um Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Use a mesma assinatura e o mesmo grupo de recursos usados para o recurso do Data Box Edge.

![Criar um recurso do Hub IoT](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Se uma função de computação de borda ainda não foi configurada, as seguintes condições se aplicam:

- O recurso do Hub IoT não tem dispositivos IoT do Azure nem dispositivos do Azure IoT Edge.
- Você não pode criar compartilhamentos locais de Borda. Quando você adiciona um compartilhamento, a opção de criar um compartilhamento local para computação de Borda não está habilitada.


## <a name="set-up-compute-role"></a>Configurar a função de computação

Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT.

Para configurar a função de computação no dispositivo, faça o seguinte:

1. Acesse o recurso Data Box Edge, selecione **Visão Geral** e, em seguida, selecione **Configurar função de computação**. 

    ![O link Visão Geral no painel esquerdo](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Opcionalmente, você pode acessar **Módulos** e selecionar **Configurar computação**.

    ![Os links "Módulos" e "Configurar computação"](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. Na lista suspensa, selecione o **recurso do Hub IoT** criado na etapa anterior.  
    No momento, somente a plataforma Linux está disponível para o dispositivo IoT Edge. 
    
1. Clique em **Criar**.

    ![Botão Criar](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    A função de computação leva alguns minutos para ser criada. Devido a um bug nesta versão, mesmo quando a função de computação é criada, a tela não é atualizada. Para confirmar se a função de computação de borda foi configurada, acesse **Módulos**.  

    ![A lista de dispositivos de "Configurar computação de borda"](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. Acesse **Visão Geral** novamente.  
    A tela é atualizada para indicar que a função de computação está configurada.

    ![Configurar uma função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. No Hub IoT que você usou quando criou a função de computação de borda, acesse **Dispositivos IoT**.  
    Agora há um dispositivo IoT habilitado. 

    ![A página "Dispositivos IoT"](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. No painel esquerdo, selecione **IoT Edge**.  
    Também há um dispositivo IoT Edge habilitado.

    ![Configurar uma função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. Selecione e clique no dispositivo IoT Edge.  
    Um agente do Edge está em execução no dispositivo do IoT Edge. 

    ![A página Detalhes do Dispositivo](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    Não há módulos personalizados neste dispositivo de borda, mas agora você pode adicionar um módulo personalizado. Para saber como criar um módulo personalizado, acesse [Desenvolver um módulo em C# para o dispositivo Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Adicionar um módulo personalizado

Nesta seção, você adiciona um módulo personalizado ao dispositivo do IoT Edge que foi criado em [Desenvolver um módulo em C# para o Data Box Edge](data-box-edge-create-iot-edge-module.md). 

O procedimento a seguir usa um exemplo em que o módulo personalizado pega arquivos de um compartilhamento local no dispositivo de borda e move esses arquivos para um compartilhamento em nuvem no dispositivo. O compartilhamento em nuvem então efetua o push dos arquivos para a conta de Armazenamento do Azure associada com o compartilhamento em nuvem. 

1. Adicione um compartilhamento local ao dispositivo de borda, fazendo o seguinte:

     a. Em seu recurso Data Box Edge, vá para **Compartilhamentos**. 
    
    b. Selecione **Adicionar compartilhamento** e, em seguida, forneça o nome do compartilhamento e selecione o tipo de compartilhamento. 
    
    c. Para criar um compartilhamento local, marque a caixa de seleção **Configurar como compartilhamento local do Edge**. 
    
    d. Selecione **Criar novo** ou **Usar existente** e, em seguida, selecione **Criar**.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Se você criou um compartilhamento NFS local, use a seguinte opção de comando rsync (compartilhamento remoto) para copiar os arquivos no compartilhamento:

    `rsync --inplace <source file path> < destination file path>`

    Para obter mais informações sobre o comando rsync, acesse a [Documentação do rsync](https://www.computerhope.com/unix/rsync.htm). 

    O compartilhamento local é criado e você receberá uma notificação de êxito na criação. A lista de compartilhamento pode ser atualizada, mas você deve aguardar a criação do compartilhamento ser concluída.
    
1. Acesse a lista de compartilhamentos. 

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. Para exibir as propriedades do compartilhamento local recém-criado, basta selecioná-lo. 

1. Na caixa **Ponto de montagem local para módulos de computação de borda**, copie o valor correspondente a esse compartilhamento.  
    Você usará esse ponto de montagem local ao implantar o módulo.

    ![A caixa "Ponto de montagem local para módulos de computação de borda"](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. Em um compartilhamento de nuvem existente, criado no dispositivo Data Box Edge, na caixa **Ponto de montagem local para módulos de computação de borda**, copie o ponto de montagem local dos módulos de computação de borda deste compartilhamento de nuvem.  
    Você usará esse ponto de montagem local ao implantar o módulo.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. Para adicionar um módulo personalizado ao dispositivo IoT Edge, acesse o recurso Hub IoT e, em seguida, acesse o **Dispositivo do IoT Edge**. 

1. Selecione o dispositivo e, em seguida, em **Detalhes do dispositivo**, selecione **Definir Módulos**. 

    ![O link Definir Módulos](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. Em **Adicionar Módulos**, faça o seguinte:

     a. Insira o nome, o endereço, o nome de usuário e a senha para as configurações do registro de contêiner do módulo personalizado.  
    O nome, o endereço e as credenciais listadas são usados para recuperar módulos com uma URL correspondente. Para implantar este módulo, em **Módulos de implantação**, selecione **Módulo do IoT Edge**. Este módulo do IoT Edge é um contêiner do Docker que você pode implantar no dispositivo do IoT Edge associado ao seu dispositivo do Data Box Edge.

    ![A página Definir Módulos](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. Especifique as configurações do módulo personalizado do IoT Edge, inserindo o nome do seu módulo e o URI de imagem da imagem de contêiner correspondente. 
    
    ![A página Módulos Personalizados do IoT Edge](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. Na caixa **Opções de Criação de Contêiner**, insira os pontos de montagem locais dos módulos do Edge que você copiou nas etapas anteriores para a nuvem e o compartilhamento local.
    > [!IMPORTANT]
    > Use os caminhos copiados; não crie novos caminhos. Os pontos de montagem locais são mapeados aos **InputFolderPath** e **OutputFolderPath** correspondentes especificados no módulo ao [atualizar o módulo com o código personalizado](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
    Na caixa **Opções de Criação de Contêiner**, cole o exemplo a seguir: 
    
    ```
    {
        "HostConfig": {
        "Binds": [
        "/home/hcsshares/mysmblocalshare:/home/LocalShare",
        "/home/hcsshares/mysmbshare1:/home/CloudShare"
        ]
        }
    }
    ```

    Forneça também variáveis de ambiente para seu módulo aqui.

    ![A caixa Opções de Criação de Contêiner](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. Se necessário, defina as configurações avançadas de tempo de execução do Edge e, em seguida, clique em **Avançar**.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  Em **Especificar Rotas**, defina rotas entre módulos.  
    Neste exemplo, insira o nome do compartilhamento local que efetuará push de dados para o compartilhamento de nuvem.

    Você pode substituir a *rota* pela seguinte cadeia de caracteres de rota:       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![A seção Especificar Rotas](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. Selecione **Avançar**. 

1.  Em **Examinar implantação**, examine todas as configurações e, em seguida, selecione **Enviar** para enviar o módulo para implantação.

    ![A página Definir Módulos](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    Essa ação inicia a implantação de módulo, conforme mostrado na imagem a seguir:

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência de dados

A etapa final é garantir que o módulo esteja conectado e funcionando como esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
Para verificar se o módulo está em execução, faça o seguinte:

1. Selecione o módulo e, em seguida, exiba o Gêmeo de Identidade do Módulo.  
    O status do cliente do módulo e do dispositivo de borda deve aparecer como *Conectado*.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    Depois que o módulo estiver em execução, ele também será exibido na lista de módulos de borda no recurso do Data Box Edge. O status do tempo de execução do módulo adicionado é *em execução*.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  No Explorador de Arquivos, conecte-se aos compartilhamentos em nuvem e locais que você criou anteriormente.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  Adicione dados ao compartilhamento de local.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    Os dados são movidos para o compartilhamento em nuvem.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    Os dados são então enviados por push do compartilhamento em nuvem para a conta de armazenamento. Para exibir os dados, acesse o Gerenciador de Armazenamento.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Você concluiu o processo de validação.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um recurso do Hub IoT
> * Configurar a função de computação
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

Para saber como administrar o dispositivo do Data Box Edge, confira:

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Data Box Edge](https://aka.ms/dbg-docs)


