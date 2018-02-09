---
title: "Como usar uma MSI (Identidade do Serviço Gerenciado) de VM Linux para acessar o Azure Data Lake Store"
description: "Um tutorial que mostra como usar uma MSI (Identidade do Serviço Gerenciado) de VM Linux para acessar o Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dc3ebf3ba232241677dc7aace4f835cee0cf54a7
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Usar uma MSI (Identidade do Serviço Gerenciado) de VM Linux para acessar o Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial mostra como usar uma MSI (Identidade do Serviço Gerenciado) para uma VM (máquina virtual) Linux para acessar um Azure Data Lake Store. As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. Você aprenderá como:

> [!div class="checklist"]
> * Habilitar MSI em uma VM Linux 
> * Conceder acesso a um Azure Data Lake Store à sua VM
> * Obter um token de acesso usando a identidade da VM e usá-lo para acessar um Azure Data Lake Store

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma Máquina Virtual do Linux em um novo Grupo de Recursos

Para este tutorial, vamos criar uma nova VM do Linux. Você também pode habilitar o MSI em uma VM existente.

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Insira as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH** ou **Senha**. As credenciais criadas permitirão que você faça logon na máquina virtual.

   ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5. Para selecionar um novo **Grupo de Recursos** no qual você deseja criar a máquina virtual, escolha **Criar Novo**. Ao concluir, clique em **OK**.
6. Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos** ou altere o filtro Tipo de disco com suporte. Na folha de configurações, mantenha os padrões e clique em **OK**.

## <a name="enable-msi-on-your-vm"></a>Habilitar o MSI na sua VM

Um MSI de máquina virtual permite obter tokens de acesso do Azure AD sem a necessidade de colocar as credenciais no seu código. Nos bastidores, habilitar a MSI faz duas coisas: instala a extensão de VM da MSI na VM e habilita a MSI no Azure Resource Manager.  

1. Selecione a **Máquina Virtual** na qual você deseja habilitar MSI.
2. Na barra de navegação à esquerda, clique em **Configuração**.
3. Você verá **Identidade de Serviço Gerenciado**. Para registrar e habilitar o MSI, selecione **Sim**; se você deseja desabilitá-la, escolha Não.
4. Lembre-se de clicar em **Salvar** para salvar a configuração.

   ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se você deseja verificar quais extensões estão nesta **VM do Linux**, clique em **Extensões**. Se o MSI estiver habilitado, **ManagedIdentityExtensionforLinux** aparecerá na lista.

   ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Conceder acesso ao Azure Data Lake Store à sua VM

Agora você pode conceder o acesso a arquivos e pastas em um Azure Data Lake Store à sua VM.  Para esta etapa, é possível usar um Data Lake Store existente ou criar um novo.  Para criar um novo Data Lake Store usando o Portal do Azure, siga este [Guia de início rápido do Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Também há guias de início rápido que usam a CLI do Azure e o Azure PowerShell na [documentação do Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

No seu Data Lake Store, crie uma nova pasta e conceda permissão à sua VM permissão da MSI para ler, gravar e executar arquivos nessa pasta:

1. No Portal do Azure, clique em **Data Lake Store** na navegação à esquerda.
2. Clique no Data Lake Store que você deseja usar para este tutorial.
3. Clique em **Data Explorer** na barra de comandos.
4. A pasta raiz do Data Lake Store é selecionada.  Clique em **Acesso** na barra de comandos.
5. Clique em **Adicionar**.  No campo **Selecionar**, insira o nome da sua VM, por exemplo **DevTestVM**.  Clique para selecionar sua VM nos resultados da pesquisa e escolha **Selecionar**.
6. Clique em **Selecionar permissões**.  Selecione **Leitura** e **Execução**, adicione a **Esta pasta** e adicione como **Somente permissão de acesso**.  Clique em **OK**.  A permissão deverá ser adicionada com êxito.
7. Feche a folha **Acesso**.
8. Para este tutorial, crie uma nova pasta.  Clique em **Nova pasta** na barra de comandos e dê um nome para a pasta, por exemplo **TestFolder**.  Clique em **OK**.
9. Clique na pasta que você criou e escolha **Acesso** na barra de comandos.
10. Semelhante à etapa 5, clique em **Adicionar** e, no campo **Selecionar**, digite o nome da sua VM, selecione-a e clique em **Selecionar**.
11. Semelhante à etapa 6, clique em **Selecionar permissões**, selecione **Leitura**, **Gravação** e **Execução**, adicione a **Esta pasta** e acrescente como **Uma entrada de permissão de acesso e uma entrada de permissão padrão**.  Clique em **OK**.  A permissão deverá ser adicionada com êxito.

A MSI de VM agora pode executar todas as operações nos arquivos da pasta que você criou.  Para saber mais sobre como gerenciar acesso ao Data Lake Store, leia este artigo sobre [Controle de acesso no Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Obter um token de acesso usando a MSI de VM e usá-la para chamar o sistema de arquivos do Azure Data Lake Store

O Azure Data Lake Store tem suporte nativo para autenticação do Azure AD, de modo que ele pode aceitar diretamente os tokens de acesso obtidos por meio do MSI.  Para autenticar para o sistema de arquivos do Data Lake Store, você enviar um token de acesso emitido pelo Azure AD para o ponto de extremidade do sistema de arquivos do Data Lake Store em um cabeçalho de autorização no formato “Bearer \<ACCESS_TOKEN_VALUE\>”.  Para saber mais sobre o suporte do Data Lake Store à autenticação do Azure AD, leia [Autenticação com o Data Lake Store usando o Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

Neste tutorial, você se autenticará na API REST do sistema de arquivos do Data Lake Store usando o CURL para fazer solicitações REST.

> [!NOTE]
> Os SDKs do cliente do sistema de arquivos do Data Lake Store ainda não dão suporte à Identidade de Serviço Gerenciado.  Este tutorial será atualizado quando esse suporte for adicionado ao SDK.

Para concluir essas etapas, você precisará do cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal, navegue até a VM do Linux e em **Visão geral**, clique em **Conectar**.  
2. **Conecte-se** à VM com um cliente SSH de sua escolha. 
3. Na janela de terminal, usando o CURL, faça uma solicitação para o ponto de extremidade da MSI local para obter um token de acesso para o sistema de arquivos do Data Lake Store.  O identificador de recursos do Data Lake Store é “https://datalake.azure.net/”.  É importante incluir a barra à direita no identificador de recursos.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Uma resposta bem-sucedida retorna o token de acesso que você usará para autenticar-se no Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Usando o CURL, faça uma solicitação para o ponto de extremidade REST do sistema de arquivos do Data Lake Store para listar as pastas na pasta raiz.  Essa é uma maneira simples de verificar se que tudo está configurado corretamente.  Copie o valor do token de acesso da etapa anterior.  É importante que a cadeia de caracteres “Bearer” no cabeçalho de autorização tenha um “B” maiúsculo.  Você pode encontrar o nome do seu Data Lake Store na seção **Visão geral** da folha Data Lake Store no Portal do Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Uma resposta bem-sucedida tem a seguinte aparência:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Agora você pode tentar carregar um arquivo para seu Data Lake Store.  Primeiramente, crie um arquivo para ser carregado.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Usando o CURL, faça uma solicitação para o ponto de extremidade REST do sistema de arquivos do Data Lake Store para carregar o arquivo para a pasta criada que você criou anteriormente.  O upload envolve um redirecionamento e o CURL segue o redirecionamento automaticamente. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Uma resposta bem-sucedida tem a seguinte aparência:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Usando outras APIs do sistema de arquivos do Data Lake Store, é possível acrescentar aos arquivos, baixar arquivos e muito mais.

Parabéns!  Você foi autenticado no sistema de arquivos do Data Lake Store usando uma MSI de VM.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para operações de gerenciamento, o Data Lake Store usa o Azure Resource Manager.  Para obter mais informações sobre como usar uma MSI de VM para autenticar-se no Resource Manager, leia [Usar uma MSI (Identidade de Serviço Gerenciado) de VM do Linux para acessar o Resource Manager](msi-tutorial-linux-vm-access-arm.md).
- Saiba mais sobre a [Autenticação com o Data Lake Store usando o Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Saiba mais sobre [Operações do sistema de arquivos no Azure Data Lake Store usando a API REST](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) ou as [APIs do sistema de arquivos WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis.md).
- Saiba mais sobre o [Controle de acesso no Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.