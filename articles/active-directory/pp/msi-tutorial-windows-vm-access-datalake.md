---
title: "Como usar uma MSI (Identidade do Serviço Gerenciado) de VM Windows para acessar o Azure Data Lake Store"
description: "Um tutorial que mostra como usar uma MSI (Identidade do Serviço Gerenciado) de VM Windows para acessar o Azure Data Lake Store."
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
ms.openlocfilehash: a0e352efb05890d824842acf2c75d8836750546f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Usar uma MSI (Identidade do Serviço Gerenciado) de VM Windows para acessar o Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial mostra como usar uma MSI (Identidade do Serviço Gerenciado) para uma VM (máquina virtual) Windows para acessar um Azure Data Lake Store. As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. Você aprenderá como:

> [!div class="checklist"]
> * Habilitar MSI em uma VM do Windows 
> * Conceder acesso a um Azure Data Lake Store à sua VM
> * Obter um token de acesso usando a identidade da VM e usá-lo para acessar um Azure Data Lake Store

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Windows em um novo grupo de recursos

Para este tutorial, vamos criar uma nova VM do Windows.  Você também pode habilitar o MSI em uma VM existente.

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 
3. Insira as informações da máquina virtual. O **Nome de usuário** e **Senha** criados aqui são as credenciais usadas para fazer logon na máquina virtual.
4. Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5. Para selecionar um novo **Grupo de Recursos** no qual será criada a máquina virtual, escolha **Criar Novo**. Ao concluir, clique em **OK**.
6. Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. Na página Configurações, mantenha os padrões e clique em **OK**.

   ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitar o MSI na sua VM 

Um MSI de VM permite obter tokens de acesso do Azure AD sem a necessidade de colocar as credenciais no seu código. Habilitar o MSI informa ao Azure para criar uma identidade gerenciada para sua VM. Nos bastidores, habilitar o MSI faz duas coisas: instala a extensão de VM do MSI em sua VM, e isso permite MSI no Azure Resource Manager.

1. Selecione a **Máquina Virtual** na qual você deseja habilitar MSI.  
2. Na barra de navegação à esquerda, clique em **Configuração**. 
3. Você verá **Identidade de Serviço Gerenciado**. Para registrar e habilitar o MSI, selecione **Sim**; se você deseja desabilitá-la, escolha Não. 
4. Lembre-se de clicar em **Salvar** para salvar a configuração.  
   ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se você deseja verificar quais as extensões que estão nessa VM, clique em **Extensões**. Se o MSI estiver habilitado, **ManagedIdentityExtensionforWindows** será exibido na lista.

   ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

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

A MSI de VM agora pode executar todas as operações nos arquivos da pasta que você criou.  Para saber mais sobre como gerenciar acesso ao Data Lake Store, leia este artigo sobre [Controle de acesso no Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Obter um token de acesso usando a MSI de VM e usá-la para chamar o sistema de arquivos do Azure Data Lake Store

O Azure Data Lake Store tem suporte nativo para autenticação do Azure AD, de modo que ele pode aceitar diretamente os tokens de acesso obtidos por meio do MSI.  Para autenticar para o sistema de arquivos do Data Lake Store, você enviar um token de acesso emitido pelo Azure AD para o ponto de extremidade do sistema de arquivos do Data Lake Store em um cabeçalho de autorização no formato “Bearer <ACCESS_TOKEN_VALUE>”.  Para saber mais sobre o suporte do Data Lake Store à autenticação do Azure AD, leia [Autenticação com o Data Lake Store usando o Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Os SDKs do cliente do sistema de arquivos do Data Lake Store ainda não dão suporte à Identidade de Serviço Gerenciado.  Este tutorial será atualizado quando esse suporte for adicionado ao SDK.

Neste tutorial, você se autenticará na API REST do sistema de arquivos do Data Lake Store usando o PowerShell para fazer solicitações REST. Para usar a MSI de VM para autenticação, será necessário fazer as solicitações da VM.

1. No portal, navegue até **Máquinas Virtuais**, acesse a VM Windows e, em **Visão geral**, clique em **Conectar**.
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows. 
3. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o **PowerShell** na sessão remota. 
4. Usando o `Invoke-WebRequest` do PowerShell, faça uma solicitação para o ponto de extremidade de MSI local para obter um token de acesso para o Azure Data Lake Store.  O identificador de recursos do Data Lake Store é “https://datalake.azure.net/”.  O Data Lake faz uma correspondência exata no identificador de recursos, por isso a barra à direita é importante.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Converta a resposta de um objeto JSON para um objeto do PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraia o token de acesso da resposta.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Usando 'Invoke-WebRequest' do PowerShell, faça uma solicitação para o ponto de extremidade REST do Data Lake Store para listar as pastas na pasta raiz.  Essa é uma maneira simples de verificar se que tudo está configurado corretamente.  É importante que a cadeia de caracteres “Bearer” no cabeçalho de autorização tenha um “B” maiúsculo.  Você pode encontrar o nome do seu Data Lake Store na seção **Visão geral** da folha Data Lake Store no Portal do Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Uma resposta bem-sucedida tem a seguinte aparência:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Agora você pode tentar carregar um arquivo para seu Data Lake Store.  Primeiramente, crie um arquivo para ser carregado.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Usando `Invoke-WebRequest` do PowerShell, faça uma solicitação para o ponto de extremidade REST do Data Lake Store para carregar o arquivo para a pasta criada que você criou anteriormente.  Duas etapas são necessárias para essa solicitação.  Na primeira etapa, você faz uma solicitação e obtém um redirecionamento para onde o arquivo deve ser carregado.  Na segunda etapa, ocorre o upload do arquivo propriamente dito.  Lembre-se de definir o nome da pasta e do arquivo corretamente se tiver usado valores diferentes deste tutorial. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Se você inspecionar o valor de `$HdfsRedirectResponse`, ele deverá se parecer com a seguinte resposta:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Conclua o upload enviando uma solicitação para o ponto de extremidade de redirecionamento:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Uma resposta bem-sucedida tem a seguinte aparência:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Usando outras APIs do sistema de arquivos do Data Lake Store, é possível acrescentar aos arquivos, baixar arquivos e muito mais.

Parabéns!  Você foi autenticado no sistema de arquivos do Data Lake Store usando uma MSI de VM.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para operações de gerenciamento, o Data Lake Store usa o Azure Resource Manager.  Para obter mais informações sobre como usar uma MSI de VM para autenticar-se no Resource Manager, leia [Usar uma MSI (Identidade de Serviço Gerenciado) de VM do Linux para acessar o Resource Manager](msi-tutorial-linux-vm-access-arm.md).
- Saiba mais sobre a [Autenticação com o Data Lake Store usando o Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Saiba mais sobre [Operações do sistema de arquivos no Azure Data Lake Store usando a API REST](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) ou as [APIs do sistema de arquivos WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Saiba mais sobre o [Controle de acesso no Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.