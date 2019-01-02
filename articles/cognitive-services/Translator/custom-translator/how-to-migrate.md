---
title: Migrar espaço de trabalho e projetos do Hub do Microsoft Translator? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Migre o espaço de trabalho e os projetos do Hub para o Tradutor Personalizado.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 6572a9b72554691441cb258a87a5db4ba7845087
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077113"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrar espaço de trabalho e projetos do Hub para o Tradutor Personalizado

É possível migrar o espaço de trabalho e os projetos do [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) facilmente para o Tradutor Personalizado. A migração é iniciada no Hub da Microsoft selecionando um espaço de trabalho ou um projeto, selecionando um espaço de trabalho no Tradutor Personalizado e, em seguida, selecionando os treinamentos que você deseja transferir.  Depois que a migração é iniciada, as configurações de treinamento selecionadas serão transferidas com todos os documentos relevantes.  Modelos implantados são treinados e podem ser implantados automaticamente após a conclusão.

Estas ações são executadas durante a migração:
* Todos os documentos e as definições do projeto terão seus nomes transferidos com a adição de "hub_" como prefixo no nome.  Os dados de teste e ajuste gerados automaticamente serão denominados hub_systemtune_<modelid> ou hub_systemtest_<modelid>.  
* Os treinamentos que estavam no estado implantado quando a migração ocorrer serão treinados automaticamente usando os documentos do treinamento do Hub.  Esse treinamento não será cobrado na sua assinatura.  Se a implantação automática foi selecionada para a migração, o modelo treinado será implantado após a conclusão. Custos de hospedagem normais serão aplicados.  
* Os treinamentos migrados que não estavam no estado implantado serão colocados no estado de rascunho migrado.   Nesse estado, você terá a opção de treinar de um modelo com a definição de migrada, mas serão aplicados os encargos de treinamento regulares.
* Em algum momento, a pontuação BLEU migrada do treinamento do Hub poderá ser encontrada na página TrainingDetails do modelo no cabeçalho “Pontuação BLEU no Hub de TA”.

>[!Note]
>Para um treinamento ter êxito, o Tradutor Personalizado requer um mínimo de 10.000 frases extraídas. Para um número menor de frases extraídas que o [mínimo sugerido](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), o Tradutor Personalizado não poderá conduzir um treinamento.

## <a name="enable-account-migration"></a>Habilitar migração de conta 

Para usar a Ferramenta de Migração, você precisará ter a migração da conta do Hub habilitada.  Para fazer isso, envie um email para [custommt@microsoft.com ](mailto:custommt@microsoft.com) com uma lista de todas as contas do LiveID que você gostaria de habilitar. Essas contas devem ser os endereços de email com os quais você faz logon.

## <a name="find-custom-translator-workspace-id"></a>Encontrar a ID do espaço de trabalho do Tradutor Personalizado

Para migrar o espaço de trabalho do [Hub do Microsoft Translator](https://hub.microsofttranslator.com/), será necessária uma ID do espaço de trabalho de destino no Tradutor Personalizado. O espaço de trabalho de destino no Tradutor Personalizado é onde todos os espaços de trabalho e projetos do Hub deverão ser migrados.

Você encontrará a ID do espaço de trabalho de destino na página Configurações do Tradutor Personalizado: 

1.  Acesse a página "Configuração" no portal do Tradutor Personalizado.

2.  Você encontrará a ID do espaço de trabalho na seção Informações Básicas.

    ![Como localizar a ID do espaço de trabalho de destino](media/how-to/how-to-find-destination-ws-id.png)

3. Mantenha a ID do espaço de trabalho de destino para consultar durante o processo de migração.

## <a name="migrate-a-project"></a>Migrar um projeto

Se você quiser migrar os projetos seletivamente, o Hub do Microsoft Translator oferecerá essa capacidade.

Para migrar um projeto:

1.  Entre no Hub do Microsoft Translator.

2.  Vá para a página "Projetos".

3.  Clique no link "Migrar" do projeto apropriado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub.png)

4.  Ao pressionar o link de migração, você verá um formulário permitindo:
   * Especificar o espaço de trabalho que deseja transferir para o Tradutor Personalizado
   * Indicar se deseja transferir todos os treinamentos bem-sucedidos ou apenas os treinamentos implantados. Por padrão, todos os treinamentos bem-sucedidos serão transferidos.
   * Indicar se deseja que o treinamento seja implantado automaticamente após a conclusão. Por padrão, o treinamento não será implantado automaticamente após a conclusão.


5.  Clique em "Enviar Solicitação".

## <a name="migrate-a-workspace"></a>Migrar um espaço de trabalho

Além de migrar um único projeto, você também poderá migrar todos os projetos com treinamentos bem-sucedidos em um espaço de trabalho.  Isso fará com que cada projeto no espaço de trabalho seja avaliado como se o link de migração tivesse sido pressionado.  Esse recurso é adequado para usuários com vários projetos que desejam migrar todos eles para o Tradutor Personalizado com as mesmas configurações.  Uma migração de espaço de trabalho pode ser iniciada na página Configurações do Hub de Tradução.

Para migrar um espaço de trabalho:

1.  Entre no Hub do Microsoft Translator.

2.  Acesse a página "Configurações".

3.  Na página "Configurações", clique em "Migrar dados do espaço de trabalho para o Tradutor Personalizado".

    ![Como migrar do Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  Na próxima página, selecione uma destas duas opções:

     a.  Somente treinamentos implantados: selecionar essa opção migrará apenas os sistemas implantados e documentos relacionados.

    b.  Todos os treinamentos bem-sucedidos: selecionar essa opção migrará todos os treinamentos e documentos relacionados.

    c.  Insira a ID do espaço de trabalho de destino no Tradutor Personalizado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Clique em Enviar Solicitação.



## <a name="migration-history"></a>Histórico de migração

Após solicitar a migração do espaço de trabalho/projeto do Hub, você localizará o histórico de migração na página Configurações do Tradutor Personalizado. 

Para exibir o histórico de migração, siga estas etapas:

1.  Acesse a página "Configuração" no portal do Tradutor Personalizado.

2.  Na seção Histórico de Migração da página Configurações, clique em Histórico de Migração.

    ![Histórico de migração](media/how-to/how-to-migration-history.png)

A página Histórico de Migração exibe as seguintes informações como resumo para cada migração solicitada.

1.  Migrados por: nome e email do usuário que enviou essa solicitação de migração

2.  Migrado em: carimbo de data/hora da migração

3.  Projetos: Número de projetos solicitados para migração vs. número de projetos migrados com êxito.

4.  Treinamentos: número de treinamentos solicitados para migração vs. número de treinamentos migrados com êxito.

5.  Documentos: O número de documentos solicitados para migração vs. número de documentos migrados com êxito.

    ![Detalhes do histórico de migração](media/how-to/how-to-migration-history-details.png)

Se quiser um relatório de migração mais detalhado sobre os projetos, treinamentos e documentos, você terá a opção de exportar os detalhes como CSV.

## <a name="implementation-notes"></a>Notas de implementação
* A migração de um projeto do Hub para o Tradutor Personalizado não terá nenhum impacto sobre os treinamentos ou projetos do Hub. Não podemos excluir projetos ou documentos do Hub durante uma migração e não podemos desfazer a implantação de modelos.
* Só é possível migrar uma vez por projeto.  Se você precisar repetir uma migração em um projeto, entre em contato conosco.
* Atualmente, o Tradutor Personalizado dá suporte a 36 idiomas que traduzem do inglês e vertem para o inglês, e estamos trabalhando duro para adicionar mais idiomas.  O Hub não exige que os modelos de linha de base e, portanto, dá suporte a várias linguagens de milhar.  Você pode migrar um par linguístico sem suporte; no entanto, faremos apenas a migração de documentos e as definições do projeto.  Não poderemos treinar o novo modelo.  Além disso, esses documentos e projetos serão exibidos como inativos para indicar que não podem ser usados no momento. Caso o suporte a esses projetos e/ou documentos seja adicionado, eles ficarão ativos e poderão ser treinados.
* Atualmente, o Tradutor Personalizado não dá suporte a dados de treinamento monolíngues.  Assim como em pares linguísticos sem suporte, você poderá migrar documentos monolíngues, mas eles aparecerão como inativos até que passem a receber suporte.  
* O Tradutor Personalizado requer 10 mil sentenças paralelas para treinamento.  O Hub da Microsoft pode treinar com um conjunto menor de dados.  Se um treinamento for migrado e não atender a esse requisito, ele será treinado.


## <a name="custom-translator-versus-hub"></a>Tradutor Personalizado versus Hub

Esta tabela compara os recursos entre o Microsoft Translator Hub e o conversor personalizado.

|   | Hub | Tradutor personalizado |
|:-----|:----:|:----:|
|Status do recurso de personalização   | Disponibilidade geral  | Disponibilidade geral |
| Versão da API de texto  | V2    | V3  |
| Personalização de SMT | SIM   | Não  |
| Personalização de NMT | Não     | SIM |
| Nova personalização de serviços de Fala unificados | Não     | SIM |
| Sem rastreamento | SIM | Sim |

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](how-to-train-model.md).
- Comece a usar o modelo de tradução personalizado implantado por meio da [API de Tradução de Texto V3 da Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
