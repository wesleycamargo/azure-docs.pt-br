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
ms.openlocfilehash: 378baad0735238dc0921e5e78e2a27b3ae907e19
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626761"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrar espaço de trabalho e projetos do Hub para o Tradutor Personalizado

É possível migrar o espaço de trabalho e os projetos do [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) para o Tradutor Personalizado. A migração começa no Hub.


Esses itens são migrados durante o processo:

1.  As definições dos projetos.

2.  A definição de treinamento será usada para criar uma nova definição de modelo no Tradutor Personalizado.

3.  Os arquivos monolíngues e paralelos usados nos treinamentos serão todos migrados como novos Documentos no Tradutor Personalizado.

4.  Os dados de ajuste e teste do sistema gerados automaticamente serão exportados e criados como novos Documentos no Tradutor Personalizado.

Para todos os treinamentos implantados, o Tradutor Personalizado treinará o modelo sem nenhum custo. Há a opção de implantá-los manualmente.

>[!Note]
>Para um treinamento ter êxito, o Tradutor Personalizado requer um mínimo de 10.000 frases extraídas. Para um número menor de frases extraídas que o [mínimo sugerido](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), o Tradutor Personalizado não poderá conduzir um treinamento.

Para todos os treinamentos com êxito, que não forem implantados, serão migrados como rascunho no Tradutor Personalizado.

## <a name="find-custom-translator-workspace-id"></a>Encontrar a ID do espaço de trabalho do Tradutor Personalizado

Para migrar o espaço de trabalho do [Hub do Microsoft Translator](https://hub.microsofttranslator.com/), será necessária uma ID do espaço de trabalho de destino no Tradutor Personalizado. O espaço de trabalho de destino no Tradutor Personalizado é onde todos os espaços de trabalho e projetos do Hub deverão ser migrados.

Você encontrará a ID do espaço de trabalho de destino na página Configurações do Tradutor Personalizado: 

1.  Acesse a página "Configuração" no portal do Tradutor Personalizado.

2.  Você encontrará a ID do espaço de trabalho na seção Informações Básicas.

    ![Como localizar a ID do espaço de trabalho de destino](media/how-to/how-to-find-destination-ws-id.png)

3. Mantenha a ID do espaço de trabalho de destino para consultar durante o processo de migração.

## <a name="migrate-workspace"></a>Migrar espaço de trabalho

Ao migrar o espaço de trabalho completo do Hub para o Tradutor Personalizado, os projetos, documentos e treinamentos serão migrados para o Tradutor Personalizado. Antes da migração, será necessário escolher se você quer migrar apenas os treinamentos implantados ou se quer migrar todos os treinamentos com êxito.

Para migrar um espaço de trabalho:

1.  Entre no Hub do Microsoft Translator.

2.  Acesse a página "Configurações".

3.  Na página "Configurações", clique em "Migrar dados do espaço de trabalho para o Tradutor Personalizado".

    ![Como migrar do Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  Na próxima página, selecione uma destas duas opções:

     a.  Apenas os treinamentos implantados: selecionar essa opção migrará apenas os sistemas implantados e documentos relacionados.

    b.  Todos os treinamentos com êxito: selecionar essa opção migrará todos os treinamentos e documentos relacionados.

    c.  Insira a ID do espaço de trabalho de destino no Tradutor Personalizado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Clique em Enviar Solicitação.

## <a name="migrate-project"></a>Migrar projeto

Se você quiser migrar os projetos seletivamente, o Hub do Microsoft Translator oferecerá essa capacidade.

Para migrar um projeto:

1.  Entre no Hub do Microsoft Translator.

2.  Vá para a página "Projetos".

3.  Clique no link "Migrar" do projeto apropriado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub.png)

4.  Na próxima página, selecione uma destas duas opções:

     a.  Apenas os treinamentos implantados: selecionar essa opção migrará apenas os sistemas implantados e documentos relacionados. 

    b.  Todos os treinamentos com êxito: selecionar essa opção migrará todos os treinamentos e documentos relacionados.

    c.  Insira a ID do espaço de trabalho de destino no Tradutor Personalizado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Clique em "Enviar Solicitação".

## <a name="migration-history"></a>Histórico de migração

Após solicitar a migração do espaço de trabalho/projeto do Hub, você localizará o histórico de migração na página Configurações do Tradutor Personalizado. 

Para exibir o histórico de migração, siga estas etapas:

1.  Acesse a página "Configuração" no portal do Tradutor Personalizado.

2.  Na seção Histórico de Migração da página Configurações, clique em Histórico de Migração.

    ![Histórico de migração](media/how-to/how-to-migration-history.png)

A página Histórico de Migração exibe as seguintes informações como resumo para cada migração solicitada.

1.  Migrado por: nome e email do usuário que enviou essa solicitação de migração

2.  Migrado em: carimbo de data/hora da migração

3.  Projetos: número de projetos solicitados para migração vs. número de projetos migrados com êxito.

4.  Treinamentos: número de treinamentos solicitados para migração vs. número de treinamentos migrados com êxito.

5.  Documentos: o número de documentos solicitados para migração vs. número de documentos migrados com êxito.

    ![Detalhes do histórico de migração](media/how-to/how-to-migration-history-details.png)

Se quiser um relatório de migração mais detalhado sobre os projetos, treinamentos e documentos, você terá a opção de exportar os detalhes como CSV.

>[!Note]
>A migração tem suporte apenas para os pares de idiomas onde existem idiomas de NMT. Verifique a lista dos [idiomas de NMT com suporte](https://www.microsoft.com/translator/business/languages/). Para os pares de idiomas onde não existem idiomas de NMT, os dados serão movidos do Hub para o Tradutor Personalizado, mas os treinamentos não poderão ser conduzidos nesses pares de idiomas.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](how-to-train-model.md).
- Comece a usar o modelo de tradução personalizado implantado por meio da [API de Tradução de Texto V3 da Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).