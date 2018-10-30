---
title: Transformar dados com o Azure Data Box Edge | Microsoft Docs
description: Saiba como configurar a função de computação no Data Box Edge e usá-la para transformar dados antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: ba77fc4596d9bb245b3cea2538804b1816e9ad14
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466963"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Tutorial: transformar dados com o Azure Data Box Edge (Versão Prévia)

Este tutorial descreve como configurar a função de computação no Data Box Edge. Depois de configurar a função de computação, o Data Box Edge pode transformar dados antes de enviar para o Azure.

Esse procedimento leva cerca de 30 a 45 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um recurso do Hub IoT
> * Configurar a função de computação
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução.
 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar computação no Data Box Edge, verifique se:

* O dispositivo Data Box Edge é ativado como detalhado em [Conectar e ativar seu Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Criar um recurso do Hub IoT

Antes de configurar a função de computação no Data Box Edge, você deve criar um recurso de Hub IoT.

Para obter instruções detalhadas, acesse [Criar um Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Use a mesma assinatura e o mesmo grupo de recursos usados para o recurso do Data Box Edge.

![Criar recurso do Hub IoT](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Quando a função de computação de borda não estiver configurada, observe que:

- O recurso do Hub IoT não tem dispositivos IoT nem dispositivos IoT Edge.
- Você não pode criar compartilhamentos locais de Borda. Quando você adiciona um compartilhamento, a opção de criar um compartilhamento local para computação de Borda não está habilitada.


## <a name="set-up-compute-role"></a>Configurar a função de computação

Quando a função de computação de Borda está configurada no dispositivo de Borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser exibidos no recurso do Hub IoT.

Para configurar a função de computação no dispositivo, siga estas etapas.

1. Vá para o recurso Data Box Edge e, em seguida, vá para **Visão Geral** e clique em **Configurar função de computação**. 

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Você também pode ir para **Módulos** e clicar em **Configurar computação**.

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. Na lista suspensa, selecione **Recursos do Hub IoT** criado na etapa anterior. Somente a plataforma Linux está disponível no momento para o dispositivo IoT Edge. Clique em **Criar**.

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. A função de computação leva alguns minutos para ser criada. Devido a um bug nesta versão, mesmo quando a função de computação é criada, a tela não é atualizada. Vá para **Módulos** e poderá ver que a computação de Borda está configurada.  

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. Vá para **Visão Geral** novamente e agora a tela é atualizada para indicar que a função de computação está configurada.

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Vá para o Hub IoT que você usou ao criar a função de computação de Borda. Vá para **Dispositivos IoT**. Você pode ver que um dispositivo IoT agora está habilitado. 

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. Vá para **IoT Edge** e verá que um dispositivo IoT Edge também está habilitado.

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. Selecione e clique no dispositivo IoT Edge. Um agente do Edge está em execução no dispositivo do IoT Edge. 

    ![Configurar a função de computação](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

Há módulos personalizados, no entanto, não neste dispositivo Edge. Agora você pode adicionar um módulo personalizado a este dispositivo. Para saber como criar um módulo personalizado, acesse [Desenvolver um módulo C# para o Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Adicionar um módulo personalizado

Nesta seção, você adicionará um módulo personalizado ao dispositivo do IoT Edge que você criou no [Desenvolver um módulo C# para o Data Box Edge](data-box-edge-create-iot-edge-module.md). 

Este procedimento usa um exemplo em que o módulo personalizado usado pega arquivos de um compartilhamento local no dispositivo do Edge e leva-os para um compartilhamento em nuvem no dispositivo. O compartilhamento em nuvem então efetua o push dos arquivos para a conta de Armazenamento do Azure associada com o compartilhamento em nuvem. 

1. A primeira etapa é adicionar um compartilhamento local no dispositivo do Edge. Em seu recurso Data Box Edge, vá para **Compartilhamentos**. Clique em **+Adicionar compartilhamento**. Forneça o nome do compartilhamento e selecione o tipo de compartilhamento. Para criar um compartilhamento local, marque a opção **Configurar como compartilhamento de local do Edge**. Selecione um **usuário existente** ou **crie um novo**. Clique em **Criar**.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Se você tiver criado um compartilhamento NFS local, use a seguinte opção de comando rsync para copiar os arquivos para o compartilhamento:

    `rsync --inplace <source file path> < destination file path>`

     Para obter mais informações sobre o comando rsync, acesse a [Documentação do rsync](https://www.computerhope.com/unix/rsync.htm). 

 
2. Depois que o compartilhamento de local tiver sido criado e recebido uma notificação de êxito na criação (a lista de compartilhamento pode ser atualizada antes, mas você deve aguardar a conclusão da criação do compartilhamento), vá para a lista de compartilhamentos. 

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. Selecione e clique no compartilhamento local criado recentemente e exiba as propriedades do compartilhamento. Copie e salve o **ponto de montagem local para os módulos do Edge** correspondente a esse compartilhamento.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Vá para um compartilhamento em nuvem existente criado em seu dispositivo Data Box Edge. Novamente, copie e salve o ponto de montagem local dos módulos de computação de borda para esse compartilhamento de nuvem. Esses pontos de montagem locais serão usados ao implantar o módulo.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. Para adicionar um módulo personalizado ao dispositivo do IoT Edge, vá para o recurso Hub IoT e, em seguida, para **Dispositivo do IoT Edge**. Selecione e clique no dispositivo. Em **Detalhes do dispositivo**, na barra de comandos na parte superior, clique em **Definir módulos**. 

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. Em **Adicionar Módulos**, execute as seguintes etapas:

    1. Forneça o **nome**, o **endereço**, o **nome de usuário** e a **senha** para **Configurações do Registro de Contêiner** para o módulo personalizado. O nome, o endereço e as credenciais listadas são usados para recuperar os módulos com uma URL correspondente. Para implantar este módulo, em **Módulos de implantação**, selecione **Módulo do IoT Edge**. Este módulo do IoT Edge é um contêiner do Docker em que você pode implantar o dispositivo do IoT Edge associado ao seu dispositivo do Data Box Edge.

        ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. Especifique as configurações para o módulo personalizado do IoT Edge. Forneça a **nome** do seu módulo e o **URI de imagem** para a imagem de contêiner correspondente. 
    
        ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. Em **Opções de criação de contêiner**, forneça os pontos de montagem locais para os módulos do Edge copiados nas etapas anteriores para o compartilhamento em nuvem e local (é importante usar esses caminhos, em vez de criar novos). Os pontos de montagem locais são mapeados às respectivas **InputFolderPath** e o **OutputFolderPath** especificadas no módulo ao [atualizar o módulo com o código personalizado](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
        Você pode copiar e colar o exemplo mostrado abaixo nas suas **Opções de criação de contêiner**: 
        
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

        Também forneça quaisquer variáveis ambientais para seu módulo aqui.

        ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. **Defina configurações avançadas de tempo de execução do Edge** se necessário e, em seguida, clique em **Avançar**.

        ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  Em **Especificar rotas**, defina rotas entre módulos. Neste caso, forneça o nome do compartilhamento do local que efetuará o push de dados para o compartilhamento de nuvem. Clique em **Próximo**.

    Você pode substituir a rota com a seguinte cadeia de caracteres de rota:       "route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  Em **Examinar implantação**, examine todas as configurações e, se estiver satisfeito, escolha **Enviar** o módulo para a implantação.

    ![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
Isso inicia a implantação de módulo, conforme mostrado pelo **Módulo Personalizado do IoT Edge** em **Módulos**.

![Adicionar módulo personalizado](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência de dados

A etapa final é garantir que o módulo esteja conectado e funcionando como esperado. Execute as seguintes etapas para verificar se o módulo está em execução.

1. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. Selecione e clique no módulo e examine **Gêmeo de Identidade do Módulo**. O status do cliente para o módulo e o dispositivo do Edge deve aparecer como **Conectado**.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  Quando o módulo estiver em execução, ele também será exibido na lista de módulos do Edge em seu recurso do Data Box Edge. O **status de tempo de execução** do módulo adicionado é **em executando**.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  Conecte-se aos compartilhamentos em nuvem e locais que você criou por meio do Explorador de Arquivos.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  Adicione dados ao compartilhamento de local.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  Os dados são movidos para o compartilhamento em nuvem.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  Os dados são então enviados do compartilhamento em nuvem para a conta de armazenamento. Vá para o Gerenciador de Armazenamento para exibir os dados.

    ![Verifique a transformação de dados](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Isso conclui o processo de validação.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Criar um recurso do Hub IoT
> * Configurar a função de computação
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

Avance para o próximo tutorial para aprender a administrar seu Data Box Edge.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Data Box Edge](http://aka.ms/dbg-docs)


