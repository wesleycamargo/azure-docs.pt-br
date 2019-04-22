---
title: 'Tutorial: Migrar dados locais para o Armazenamento do Azure usando o AzCopy | Microsoft Docs'
description: Neste tutorial, você usará o AzCopy para migrar ou copiar dados de e para blobs, tabelas e arquivos. Migre dados com facilidade do armazenamento local para o Armazenamento do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 40138a69baf9cd621b2f287b2fe035225bfd9bec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877476"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Tutorial: Migrar dados locais para o armazenamento em nuvem usando o AzCopy

O AzCopy é uma ferramenta de linha de comando para copiar dados para ou do Armazenamento de Blobs do Azure, Arquivos do Azure e Armazenamento de Tabelas do Azure, usando comandos simples. Os comandos são projetados para desempenho ideal. Ao usar AzCopy, você pode copiar dados entre um sistema de arquivos e uma conta de armazenamento, ou entre contas de armazenamento. O AzCopy pode ser usado para copiar dados localmente para uma conta de armazenamento.

Baixe a versão do AzCopy adequada a seu sistema operacional:

* O [AzCopy no Linux](storage-use-azcopy-linux.md) é criado com o .NET Core Framework. Ele se destina a plataformas Linux, oferecendo opções de linha de comando de estilo POSIX. 
* O [AzCopy no Windows](storage-use-azcopy.md) é criado com o .NET Framework. Ele oferece opções de linha de comando estilo Windows. 
 
Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento. 
> * Use o AzCopy para carregar todos os seus dados.
> * Modifique os dados para fins de teste.
> * Crie uma tarefa agendada ou trabalho de Cron para identificar novos arquivos a serem carregados.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, baixe a versão mais recente do AzCopy em [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) ou [Windows](https://aka.ms/downloadazcopy).

Se você está no Windows, precisa ter o [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), já que este tutorial o usa para agendar uma tarefa. Os usuários do Linux usarão o comando crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Criar um contêiner

A primeira etapa é criar um contêiner, já que os blobs sempre devem ser carregados em um contêiner. Contêineres são usados como uma forma de organizar grupos de blobs, assim como pastas são usadas em um computador para organizar arquivos. 

Execute estas etapas para criar um contêiner:

1. Selecione o botão **Contas de armazenamento** na página principal e, em seguida, selecione a conta de armazenamento que você criou.
2. Selecione **Blobs** em **Serviços** e, em seguida, selecione **Contêiner**. 

   ![Criar um contêiner](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Nomes de contêiner devem começar com uma letra ou número. Eles podem conter apenas letras, números e o caractere de hífen (-). Para obter mais regras sobre como nomear blobs e contêineres, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Carregar conteúdo de uma pasta para o Armazenamento de Blobs

Você pode usar AzCopy para carregar todos os arquivos em uma pasta para Armazenamento de Blobs em [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Para carregar todos os blobs em uma pasta, digite o seguinte comando do AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

Substitua `<key>` e `key` pela sua chave de conta. No Portal do Azure, você pode recuperar a chave de conta selecionando **Chaves de acesso** em **Configurações** em sua conta de armazenamento. Selecione uma chave e, em seguida, cole-a no comando AzCopy. Se o contêiner de destino especificado não existir, o AzCopy o criará e carregará o arquivo nele. Atualize o caminho de origem para o diretório de dados e substituir **myaccount** na URL de destino com o nome da sua conta de armazenamento.

Para carregar o conteúdo do diretório especificado recursivamente do Armazenamento de Blobs, especifique a opção `--recursive` (Linux) ou `/S` (Windows). Quando você executa o AzCopy com uma dessas opções, todas as subpastas e os respectivos arquivos são carregados também.

## <a name="upload-modified-files-to-blob-storage"></a>Carregar os arquivos modificados no Armazenamento de Blobs

Você pode usar AzCopy para [carregar arquivos](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) com base na hora da sua última modificação. Para testar isso, modifique ou crie novos arquivos em seu diretório de origem para fins de teste. Para carregar apenas arquivos novos ou atualizados, adicione o parâmetro `--exclude-older` (Linux) ou `/XO` (Windows) ao comando AzCopy.

Se você quiser copiar apenas os recursos de origem que não existem no destino, especifique ambos os parâmetros, `--exclude-older` e `--exclude-newer` (Linux), ou `/XO` e `/XN` (Windows), no comando AzCopy. O AzCopy carrega apenas os dados atualizados, com base em seu carimbo de data/hora.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task"></a>Criar uma tarefa agendada

Você pode criar uma tarefa agendada ou trabalho de Cron que execute um script de comando AzCopy. O script identifica e carrega novos dados locais para o armazenamento em nuvem em um intervalo de tempo específico.

Copie o comando AzCopy para um editor de texto. Atualize os valores de parâmetro do comando AzCopy para os valores apropriados. Salve o arquivo como `script.sh` (Linux) ou `script.bat` (Windows) para AzCopy.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

O AzCopy é executado com a opção detalhada `--verbose` (Linux) ou `/V` (Windows). A saída é redirecionada para um arquivo de log.

Neste tutorial, [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) é usado para criar uma tarefa agendada no Windows. O comando [Crontab](http://crontab.org/) é usado para criar um trabalho Cron no Linux.
 **Schtasks** permite que um administrador crie, exclua, consulte, altere, execute e finalize as tarefas agendadas em um computador local ou remoto. **Cron** permite que os usuários do Linux e Unix executem comandos ou scripts em uma data e hora especificadas usando [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Para criar um trabalho Cron no Linux, digite o seguinte comando em um terminal:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Especificar a expressão Cron `*/5 * * * *` no comando indica que o script de shell `script.sh` deve ser executado a cada cinco minutos. Você pode agendar o script para ser executado em um momento específico diariamente, mensalmente ou anualmente. Para saber mais sobre como definir a data e hora para a execução do trabalho, consulte [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Para criar uma tarefa agendada no Windows, digite o seguinte comando no prompt de comando ou no PowerShell:

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

O comando usa:
- O parâmetro `/SC` para especificar um agendamento a ser executado a cada um minuto.
- O parâmetro `/MO` para especificar um intervalo de cinco minutos.
- O parâmetro `/TN` para especificar o nome da tarefa.
- O parâmetro `/TR` para especificar o caminho para o arquivo `script.bat`.

Para saber mais sobre como criar uma tarefa agendada no Windows, consulte [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Para validar que o trabalho cron/tarefa agendada seja executada corretamente, crie novos arquivos no seu diretório `myfolder`. Aguarde cinco minutos para confirmar que os novos arquivos foram carregados para sua conta de armazenamento. Vá para o diretório de log para exibir os logs de saída da tarefa agendada ou do trabalho Cron.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as maneiras de mover dados locais para o Armazenamento do Azure e vice-versa, siga este link:

> [!div class="nextstepaction"]
> [Mover dados de e para o Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Para obter mais informações especificamente sobre o AzCopy, escolha o artigo apropriado para seu sistema operacional:

> [!div class="nextstepaction"]
> [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md)
> [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md)
