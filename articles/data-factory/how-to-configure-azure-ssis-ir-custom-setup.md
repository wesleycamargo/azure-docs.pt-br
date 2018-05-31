---
title: Configuração personalizada para o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Este artigo descreve como usar a interface de instalação personalizada para o tempo de execução de integração do Azure SSIS
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: ff47060ddfee458279c9fed0fd3fcafcf35229d2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885431"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Instalação personalizada do tempo de execução de integração do Azure-SSIS

A interface de instalação personalizada para o tempo de execução de integração do Azure SSIS permite que você altere o padrão operacional configuração ou ambiente (por exemplo, para iniciar serviços adicionais do Windows) ou instalar componentes adicionais (por exemplo, assemblies, drivers, ou extensões) em cada nó do IR do Azure-SSIS. Em geral, ele fornece uma interface para adicionar suas próprias etapas de instalação durante o provisionamento ou reconfigurar o IR do Azure-SSIS.

Você configura a configuração personalizada preparando um script e seus arquivos associados e carregando-os em um contêiner de blob em sua conta de Armazenamento do Azure. Você fornece uma assinatura de acesso compartilhado (SAS) URI (Uniform Resource Identifier) para seu contêiner quando provisiona ou reconfigura o IR do Azure-SSIS. Cada nó de IR do Azure-SSIS, em seguida, baixa o script e seus arquivos associados do seu contêiner e executa a configuração personalizada com privilégios elevados. Quando a instalação personalizada é concluída, cada nó carrega a saída padrão de execução e outros logs em seu contêiner.

Você pode instalar componentes livres ou não licenciados e componentes pagos ou licenciados. Se você for um ISV, consulte [Como desenvolver componentes pagos ou licenciados para o IR do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Limitações atuais

-   Se você quiser usar `gacutil.exe` para instalar assemblies no Cache de Assembly Global (GAC), é necessário precisa fornecê-lo como parte de sua instalação personalizada, ou usar a cópia fornecida no contêiner de visualização pública.

-   Se você precisa unir o IR do Azure-SSIS com a instalação personalizada para uma VNet, há suporte apenas para a VNet do Azure Resource Manager. Não há suporte para a VNet clássica.

-   Atualmente, não há suporte para o compartilhamento administrativo no IR do Azure-SSIS.

-   Se você deseja mapear um compartilhamento de arquivos para uma unidade em sua instalação personalizada, não há suporte para o comando `net use` no momento. Como resultado, você não pode usar um comando como `net use d: \\fileshareserver\sharename`. Em vez disso, use o comando `cmdkey` – por exemplo, `cmdkey /add:fileshareserver /user:yyy /pass:zzz` – para acessar a `\\fileshareserver\folder` diretamente nos pacotes.

## <a name="prerequisites"></a>pré-requisitos

Para personalizar o IR do Azure-SSIS, você precisa do seguinte:

-   [Assinatura do Azure](https://azure.microsoft.com/)

-   [Um Banco de Dados SQL do Azure ou servidor de Instância Gerencia](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Provisionar o IR do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Uma conta de Armazenamento do Azure](https://azure.microsoft.com/services/storage/). Para instalação personalizada, você carrega e armazena seu script de instalação personalizada e seus arquivos associados em um contêiner de blob. O processo de instalação personalizado também carrega seus logs de execução no mesmo contêiner de blob.

## <a name="instructions"></a>Instruções

2.  Baixe e instale o [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (versão 5.4 ou posterior).

3.  Prepare o script de instalação personalizado e seus arquivos associados (por exemplo, arquivos .bat, .cmd, .exe, .dll, .msi ou .ps1).

    1.  Você deve ter um arquivo de script chamado `main.cmd`, que é o ponto de entrada de sua instalação personalizada.

    2.  Se você quiser logs adicionais gerados por outras ferramentas (por exemplo, `msiexec.exe`) para ser carregado em seu contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR` como a pasta de log em seus scripts (por exemplo, `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Baixe, instale e execute o [Azure Storage Explorer](http://storageexplorer.com/).

    1.  No **(Local e Anexo)**, selecione com o botão de **Contas de Armazenamento** e selecione **Conecte-se ao Armazenamento do Azure**.

       ![Conectar-se ao armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Selecione **Usar um nome e uma chave da conta de armazenamento** e selecione **Próximo**.

       ![Usar um nome e uma chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Digite seu nome e a chave da conta de armazenamento do Azure, selecione **Próximo**e, em seguida, selecione **Conectar**.

       ![Forneça o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  Em sua conta de armazenamento do Azure conectada, clique duas vezes em **Contêineres de Blob**, selecione **Criar contêiner de Blob**e nomeie o novo contêiner.

       ![Criar um contêiner de blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Selecione o novo contêiner e carregue o script de instalação personalizado e seus arquivos associados. Certifique-se de carregar `main.cmd` no nível superior do contêiner, não em qualquer pasta. 

       ![Carregar arquivos no contêiner de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Clique com o botão direito no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**.

       ![Obter Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Crie o URI de SAS para seu contêiner com um período de expiração suficientemente longo e leitura + escrita + lista de permissões. É necessário que o URI de SAS baixe e execute o script de instalação personalizado e seus arquivos associados, sempre que qualquer nó de IR do Azure-SSIS for criado. Você precisa gravar permissão para carregar os logs de execução da instalação.

       ![Gerar Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Copie e salve o URI de SAS do seu contêiner.

       ![Copie e salve a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  Quando você provisionar ou reconfigurar o IR do Azure-SSIS com o PowerShell, antes de iniciar o IR do Azure-SSIS, execute o cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` com o URI de SAS do seu contêiner como o valor para o novo parâmetro `SetupScriptContainerSasUri`. Por exemplo: 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  Depois de concluir a configuração personalizada e iniciar o IR do Azure-SSIS, você pode encontrar a saída padrão de `main.cmd` e outros logs de execução na pasta `main.cmd.log` do seu contêiner de armazenamento.

2.  Para obter outros exemplos de instalação personalizada, conecte-se ao contêiner de visualização pública com o Azure Storage Explorer.

    a.  Em **(Local e conectado)**, clique com botão direito em **Contas de Armazenamento**, selecione **Conectar ao armazenamento do Azure**, selecione **Usar uma cadeia de caracteres de conexão ou uma URI de assinatura de acesso compartilhado** e, em seguida, selecione **Próximo**.

       ![Conecte-se ao armazenamento do Azure com a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Selecione **Usar um URI de SAS** e insira o seguinte URI de SAS para o contêiner de Visualização Pública. Selecione **Próximo**e selecione **Conectar**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Fornecer a Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Selecione o contêiner de Visualização Pública conectado e clique duas vezes na pasta `CustomSetupScript`. Nesta pasta, você encontrará os seguintes itens:

       1. Uma pasta `Sample`, que contém uma instalação personalizada para instalar uma tarefa básica em cada nó do seu IR do Azure-SSIS. A tarefa apenas executará a suspensão por alguns segundos. A pasta também contém um `gacutil` pasta, que contém `gacutil.exe`.

       2. Uma pasta `UserScenarios`, que contém várias configurações personalizadas para cenários de usuário real.

    ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Clique duas vezes na pasta `UserScenarios`. Nesta pasta, você encontrará os seguintes itens:

       1. Um `.NET FRAMEWORK 3.5` pasta, que contém uma instalação personalizada para instalar uma versão anterior do .NET Framework que podem ser necessária para componentes personalizados em cada nó do seu IR do Azure-SSIS.

       2. Uma pasta `BCP`, que contém uma instalação personalizada para instalar utilitários de linha de comando do SQL Server (`MsSqlCmdLnUtils.msi`), incluindo o programa de cópia em massa (`bcp`), em cada nó do seu IR do Azure-SSIS.

       3. Uma pasta `EXCEL`, que contém uma instalação personalizada para instalar assemblies de código-fonte aberto (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` e `ExcelDataReader.dll`) em cada nó do seu IR do Azure-SSIS.

       4. Uma pasta `MSDTC`, que contém uma instalação personalizada para modificar a rede e as configurações de segurança para a instância do Coordenador de Transação Distribuída (MSDTC) em cada modo do seu IR Azure-SSIS.

       5. Uma pasta `ORACLE ENTERPRISE`, que contém um script de instalação personalizada (`main.cmd`) e o arquivo de configuração de instalação silenciosa (`client.rsp`) para instalar o driver Oracle OCI em cada nó do IR do Azure-SSIS Enterprise Edition. Essa configuração permite o uso do Gerenciador de Conexão do Oracle, Origem e Destino. Primeiro, baixe o último cliente do Oracle – por exemplo, `winx64_12102_client.zip` – na [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) e, em seguida, faça upload dele junto com `main.cmd` e `client.rsp` no contêiner. Se você usar TNS para se conectar ao Oracle, você também precisa baixar `tnsnames.ora`, editá-lo e carregá-lo no seu contêiner, portanto ele pode ser copiado para a pasta de instalação do Oracle durante a instalação.

       6. Uma pasta `ORACLE STANDARD`, que contém um script de instalação personalizada (`main.cmd`) para instalar o driver do Oracle ODP.NET em cada nó do seu IR do Azure-SSIS. Essa configuração permite o uso do Gerenciador de Conexão ADO.NET, Origem e Destino. Primeiro, baixe o último cliente do Oracle ODP.NET – por exemplo, `ODP.NET_Managed_ODAC122cR1.zip` – na [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) e, em seguida, faça upload dele junto com `main.cmd` no contêiner.

       7. Uma pasta `SAP BW`, que contém um script de instalação personalizada (`main.cmd`) para instalar o assembly de conector do SAP .NET (`librfc32.dll`) em cada nó do IR do Azure-SSIS Enterprise Edition. Essa configuração permite o uso do Gerenciador de Conexão do SAP BW, Origem e Destino. Primeiro, carregue a versão 64 bits ou 32 bits de `librfc32.dll` da pasta de instalação do SAP em seu contêiner, junto com `main.cmd`. O script, em seguida, copia o assembly do SAP para a pasta `%windir%\SysWow64` ou `%windir%\System32` durante a instalação.

       8. Uma pasta `STORAGE`, que contém uma instalação personalizada para instalar o Azure PowerShell em cada nó do seu IR do Azure-SSIS. Essa configuração permite que você implante e execução de pacotes SSIS que executam [scripts do PowerShell para manipular a sua conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copie `main.cmd`, uma amostra `AzurePowerShell.msi` (ou instale a versão mais recente), e `storage.ps1` para seu contêiner. Use PowerShell.dtsx como um modelo para seus pacotes. O modelo de pacote combina uma [Tarefa de Download de Blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que baixa `storage.ps1` como um script do PowerShell pode ser modificado e uma [Tarefa de Executar Processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) que executa o script em cada nó.

       9. Uma pasta `TERADATA`, que contém um script de instalação personalizada (`main.cmd)`), seu arquivo associado (`install.cmd`) e pacotes do instalador (`.msi`). Esses arquivos instalam o driver ODBC, a API TPT e os conectores Teradata em cada nó do IR do Azure-SSIS Enterprise Edition. Essa configuração permite o uso do Gerenciador de Conexão do Terada, Origem e Destino. Primeiro, baixe o arquivo zip Teradata Tools and Utilities (TTU) 15.x (por exemplo, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) de [Teradata](http://partnerintelligence.teradata.com) e, em seguida, carregue-o junto com os aquivos `.cmd` e `.msi` acima em seu contêiner.

    ![Pastas na pasta de cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Para testar esses exemplos de instalação personalizada, copie e cole o conteúdo da pasta selecionada em seu contêiner. Quando você provisionar ou reconfigurar o IR do Azure-SSIS com o PowerShell, execute o cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` com o URI de SAS do seu contêiner como o valor para o novo parâmetro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Próximas etapas

-   [Enterprise Edition do Tempo de Execução de Integração do Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o Tempo de Execução de Integração do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)