---
title: Copiar dados para o Microsoft Azure Data Box por SMB | Microsoft Docs
description: Saiba como copiar dados para o Azure Data Box por meio do Serviço de cópia de dados
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: a71635abd036bb89546dd3421af97cd9b88f4327
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54439854"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>Tutorial: Usar o serviço de cópia de dados para ingerir dados diretamente no Azure Data Box (versão prévia)

Este tutorial descreve como ingerir dados usando o serviço de cópia de dados sem a necessidade de um host intermediário. O serviço de cópia de dados é executado localmente no Data Box, conecta-se ao dispositivo NAS por meio do SMB e copia os dados para o Data Box.

Use o serviço de cópia de dados:

- Nos ambientes NAS (armazenamento conectado à rede) em que os hosts intermediários podem não estar disponíveis.
- Com arquivos pequenos que levam semanas para a ingestão e para o upload dos dados. Esse serviço melhora significativamente o tempo de upload e de ingestão.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Copiar dados para caixa de dados
> * Prepare-se para enviar a caixa de dados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**.
3. Você tem as credenciais do dispositivo NAS de origem ao qual você se conecta para cópia de dados.
4. Você está conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se não houver uma conexão de 10 GbE disponível, use um link de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="copy-data-to-data-box"></a>Copiar dados para caixa de dados

Quando você estiver conectado ao NAS, a próxima etapa será copiar dados. Antes de começar a cópia de dados, examine as considerações a seguir:

- Durante a cópia de dados, verifique se o tamanho dos dados está em conformidade com os limites de tamanho descritos nos [Limites do Armazenamento do Azure e do Data Box](data-box-limits.md).
- Se os dados carregados pelo Data Box forem carregados simultaneamente por outros aplicativos fora do Data Box, então isso poderá causar falhas no trabalho de upload e dados corrompidos.
- Se os dados forem movidos à medida que o serviço de cópia de dados os lê, você poderá enfrentar falhas ou corrupção de dados.

Para copiar dados usando o serviço de cópia de dados, é necessário criar um trabalho. Siga estas etapas para criar um trabalho para copiar dados.

1. Na IU da Web local do Data Box, acesse **Gerenciar > Copiar dados**.
2. Na página **Copiar dados**, clique em **Criar**.

    ![Clique em **Criar** na página **Copiar dados**](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Na caixa de diálogo **Configurar e iniciar**, forneça as entradas a seguir.
    
    |Campo                          |Valor    |
    |-------------------------------|---------|
    |Nome do trabalho                       |Um único nome com menos de 230 caracteres para o trabalho. Estes caracteres não são permitidos no nome do trabalho – \<, \>, \|, \?, \*, \\, \:, \/ e \\\.         |
    |Local de origem                |Forneça o caminho do SMB para a fonte de dados no formato: `\\<ServerIPAddress>\<ShareName>` ou `\\<ServerName>\<ShareName>`.        |
    |Nome de Usuário                       |O nome de usuário para acessar a fonte de dados.        |
    |Senha                       |A senha para acessar a fonte de dados.           |
    |Conta de armazenamento de destino    |Na lista suspensa, selecione a conta de armazenamento de destino para a qual carregar os dados.         |
    |Tipo de armazenamento de destino       |Selecione o tipo de armazenamento de destino do blob de blocos, blob de páginas ou Arquivos do Azure.        |
    |Compartilhamento/contêiner de destino    |Insira o nome do contêiner ou do compartilhamento para carregar dados na conta de armazenamento de destino. O nome pode ser um nome do compartilhamento ou um nome de contêiner. Por exemplo, `myshare` ou `mycontainer`. Também é possível inserir no formato `sharename\directory_name` ou `containername\virtual_directory_name` na nuvem.        |
    |Copiar arquivos que correspondem a um padrão    | Insira o nome do arquivo que corresponde a um padrão das duas maneiras a seguir.<ul><li>**Usar expressões curinga** Apenas `*` e `?` são compatíveis com expressões curinga. Por exemplo, essa expressão `*.vhd` corresponde a todos os arquivos que têm a extensão .vhd. Da mesma forma, `*.dl?` corresponde a todos os arquivos cuja extensão é `.dl` ou `.dll`. Além disso, `*foo` corresponderá a todos os arquivos cujos nomes terminam com `foo`.<br>É possível inserir diretamente a expressão curinga no campo. Por padrão, o valor inserido no campo é tratado como expressão curinga.</li><li>**Usar expressões regulares** – há suporte para expressões regulares baseadas em POSIX. Por exemplo, uma expressão regular `.*\.vhd` corresponderá a todos os arquivos que têm a extensão `.vhd`. Para a expressão regular, forneça o `<pattern>` diretamente como `regex(<pattern>)`. <li>Para saber mais sobre expressões regulares, acesse [Linguagem de expressões regulares – referência rápida](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |Otimização de arquivos              |Quando habilitada, os arquivos são incluídos na ingestão. Isso agiliza a cópia de dados para arquivos pequenos.        |
 
4. Clique em **Iniciar**. As entradas são validadas e, se a validação for bem-sucedida, um trabalho será iniciado. Talvez demore alguns minutos para o trabalho iniciar.

    ![Iniciar um trabalho da caixa de diálogo Configurar trabalho e iniciar](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. É criado um trabalho com as configurações especificadas. Marque a caixa de seleção e, em seguida, será possível pausar, retomar, cancelar ou reiniciar um trabalho.

    ![Gerenciar um trabalho por meio da página copiar dados](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Será possível pausar este trabalho se ele estiver afetando os recursos NAS durante os horários de pico.

        ![Pausar um trabalho](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        É possível retomar o trabalho posteriormente, fora do horário de pico.

        ![Retomar um trabalho](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - É possível cancelar um trabalho a qualquer momento.

        ![Cancelar um trabalho](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png) É necessária uma confirmação ao cancelar um trabalho.

        ![Confirmar cancelamento do trabalho](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Se você decidir cancelar um trabalho, os dados já copiados não serão excluídos. Para excluir quaisquer dados copiados em seu Data Box, redefina o dispositivo.

        ![Redefinir dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Se você cancelar ou pausar um trabalho, os grandes arquivos que estão sendo copiados poderão ser copiados pela metade. Esses arquivos são carregados no mesmo estado para o Azure. Ao tentar cancelar ou pausar, valide que seus arquivos foram corretamente copiados. Para validar os arquivos, examine os compartilhamentos SMB ou baixe o arquivo BOM.

    - Será possível reiniciar um trabalho se ele tiver falhado abruptamente devido a um erro transitório, como uma falha de rede. Não será possível reiniciar um trabalho se ele tiver atingido um estado terminal, como concluído com êxito ou concluído com erros. As falhas poderão ocorrer devido a problemas de tamanho do arquivo ou de nomenclatura do arquivo. Esses erros são registrados, mas o trabalho não poderá ser reiniciado depois de concluído.

        ![Reiniciar um trabalho com falha](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Se você enfrentar uma falha e o trabalho não puder ser reiniciado, baixe os logs de erros e examine a falha nos arquivos de log. Depois de corrigir o problema, será possível criar um trabalho para copiar os arquivos. Também é possível [copiar os arquivos via SMB](data-box-deploy-copy-data.md).
    
    - Nesta versão, não é possível excluir um trabalho.
    
    - É possível criar trabalhos ilimitados, mas executar no máximo 10 trabalhos em paralelo em determinado momento.
    - Se a otimização do arquivo estiver ativada, os arquivos pequenos serão incluídos na ingestão para aprimorar o desempenho da cópia. Nesses casos, você verá um arquivo compactado (GUID como nome) conforme mostrado na captura de tela a seguir.

        ![Exemplo de um arquivo compactado](media/data-box-deploy-copy-data-via-copy-service/packed-file-on-ingest.png)

6. Enquanto o trabalho está em andamento, na página **Copiar dados**:

    - Na coluna **Status**, é possível exibir o status do trabalho de cópia. O status pode ser:
        - **Executando**
        - **Com falha**
        - **Êxito**
        - **Pausando**
        - **Em pausa**
        - **Cancelando**
        - **Cancelado**
        - **Concluído com erros**
    - Na coluna **Arquivos**, é possível ver o número e o tamanho do total de arquivos que está sendo copiado.
    - Na coluna **Processados**, é possível ver o número e o tamanho dos arquivos processados.
    - Na coluna **Detalhes**, clique em **Exibir** para ver os detalhes do trabalho.
    - Se você tiver quaisquer erros durante o processo de cópia, conforme mostrado na coluna **N º de erros**, acesse a coluna **Log de erros** e baixe os logs de erros para solucionar problemas.

Aguarde a conclusão dos trabalhos de cópia. Conforme alguns erros são registrados apenas na página **Conectar e copiar**, certifique-se de que os trabalhos de cópia foram concluídos sem erro antes de passar para a próxima etapa.

![Nenhum erro na página **Conectar e copiar**](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Para garantir a integridade dos dados, a soma de verificação é computada em linha à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço usado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e usado no painel](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Após a conclusão do trabalho de cópia, será possível acessar **Preparar para o envio**.

>[!NOTE]
> A Preparação para o envio não poderá ser executada enquanto os trabalhos de cópia estiverem em andamento.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Copiar dados para caixa de dados
> * Preparar para o envio de dados de caixa

Avance para o próximo tutorial para saber como enviar o Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

