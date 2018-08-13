---
title: Solução de problemas do Azure Data Box | Microsoft Docs
description: Descreve como solucionar os problemas observados no Azure Data Box Disk.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/03/2018
ms.author: alkohli
ms.openlocfilehash: e1a5cb33bb473daf5b9e45e7c64bcb297eca7733
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595538"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk-preview"></a>Solucionar problemas no Azure Data Box Disk (versão prévia)

Este artigo se aplica ao Microsoft Azure Data Box que está executando a versão prévia. Este artigo descreve alguns dos fluxos de trabalho complexos e tarefas de gerenciamento que podem ser executadas na Data Box e Data Box Disk. 

Você pode gerenciar o Disco do Data Box pelo portal do Azure. Este artigo se concentra nas tarefas que você pode executar usando o portal do Azure. Use o Portal do Azure para gerenciar pedidos, gerenciar dispositivos e acompanhar o status do pedido, conforme ele progride para a conclusão.

Este artigo inclui os seguintes tutoriais:

- Fazer download de logs de diagnóstico
- Consultar logs de atividade


> [!IMPORTANT]
> O Data Box está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução.

## <a name="download-diagnostic-logs"></a>Fazer download de logs de diagnóstico

Se houver erros durante o processo de cópia de dados, o portal exibirá um caminho até a pasta onde estão os logs de diagnóstico. 

Os logs de diagnóstico podem ser:
- Logs de erros
- Logs detalhados  

Para navegar até o caminho do log de cópia, vá até a conta de armazenamento associada ao seu pedido do Data Box. 

1.  Vá até **Geral > Detalhes do pedido** e anote a conta de armazenamento associada ao seu pedido.
 

2.  Vá até **Todos os recursos** e procure a conta de armazenamento identificada na etapa anterior. Selecione e clique na conta de armazenamento.

    ![Copiar logs 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Vá até **Serviço Blob > Procurar blobs** e procure o blob correspondente à conta de armazenamento. Acesse **diagnosticslogcontainer > waies**. 

    ![Copiar logs 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Você deve ver os logs de erros e os logs detalhados da cópia dos dados. Selecione e clique em cada arquivo e, em seguida, baixe uma cópia local.

## <a name="query-activity-logs"></a>Consultar logs de atividade

É possível usar os logs de atividade para encontrar um erro ao solucionar problemas ou monitorar como um usuário em sua organização modificou um recurso. Com os logs de atividade, você pode determinar:

- Quais operações foram executadas nos recursos em sua assinatura.
- Quem iniciou a operação. 
- Quando a operação ocorreu.
- O status da operação.
- Os valores de outras propriedades que podem ajudar você a pesquisar a operação.

O log de atividades contém todas as operações de gravação (como PUT, POST, DELETE) realizadas em seus recursos, mas não as operações de leitura (como GET). 

Os logs de atividade são retidos por 90 dias. Você pode consultar qualquer intervalo de datas, desde que a data inicial não seja anterior a 90 dias no passado. Você também pode filtrar por uma das consultas internas no Insights. Por exemplo, clique em erro e, depois, selecione e clique em falhas específicas para entender a causa raiz.

## <a name="data-box-disk-unlock-tool-errors"></a>Erros da ferramenta de desbloqueio do Data Box Disk


| Mensagem de erro/Comportamento da ferramenta      | Recomendações                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Nenhum<br><br>A ferramenta de desbloqueio do Data Box Disk trava.                                                                            | BitLocker não instalado. Verifique se o computador host que está executando a ferramenta de desbloqueio do Data Box Disk tem o BitLocker instalado.                                                                            |
| O .NET Framework atual não tem suporte. As versões com suporte são 4.5 e superiores.<br><br>A ferramenta é encerrada com uma mensagem.  | O .NET 4.5 não está instalado. Instale o .NET 4.5 ou superior no computador host que executa a ferramenta de desbloqueio do Data Box Disk.                                                                            |
| Não foi possível desbloquear ou verificar os volumes. Contatar Suporte da Microsoft  <br><br>A ferramenta não consegue desbloquear ou verificar qualquer unidade bloqueada. | A ferramenta não conseguiu desbloquear as unidades bloqueadas com a chave de acesso fornecida. Contate o Suporte da Microsoft para as próximas etapas.                                                |
| Os volumes a seguir foram desbloqueados e verificados. <br>Letras de unidade de volume: E:<br>Não foi possível desbloquear os volumes com as seguintes chaves de acesso: werwerqomnf, qwerwerqwdfda <br><br>A ferramenta desbloqueia algumas unidades e lista as letras de unidade bem-sucedidas e com falha.| Êxito parcial. Não foi possível desbloquear algumas unidades com a chave de acesso fornecida. Contate o Suporte da Microsoft para as próximas etapas. |
| Não foi possível localizar os volumes bloqueados. Verifique se o disco recebido da Microsoft está conectado corretamente e está em estado bloqueado.          | A ferramenta não consegue encontrar unidades bloqueadas. Ou as unidades já estão desbloqueadas ou não foram detectadas. Verifique se as unidades estão conectadas e bloqueadas.                                                           |
| Erro fatal: parâmetro inválido<br>Nome do parâmetro: nvalid_arg<br>USO:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Exemplo: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Exemplo: DataBoxDiskUnlock /SystemCheck<br>Exemplo: DataBoxDiskUnlock /Help<br><br>/PassKeys:       obtenha essa chave de acesso do pedido do Azure DataBox Disk. A chave de acesso desbloqueia seus discos.<br>/Help:           esta opção fornece ajuda sobre o uso de cmdlet e exemplos.<br>/SystemCheck:    esta opção verifica se o seu sistema atende aos requisitos para executar a ferramenta.<br><br>Pressione qualquer tecla para sair. | Parâmetro inválido inserido. Os únicos parâmetros permitidos são /SystemCheck, /PassKey e /Help.                                                                            |
## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar o Data Box Disk por meio do Portal do Azure](data-box-portal-ui-admin.md).
