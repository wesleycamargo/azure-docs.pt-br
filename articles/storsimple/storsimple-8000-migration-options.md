---
title: Avaliar opções para migrar dados do StorSimple séries 5000-7000| Microsoft Docs
description: Fornece uma visão geral das opções para migrar dados do StorSimple séries 5000-7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/30/2018
ms.author: alkohli
ms.openlocfilehash: 5166e154e1a67cea777933b6bf8757661a9c3ad5
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242952"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opções para migrar dados do StorSimple séries 5000-7000 

> [!IMPORTANT]
> Em 31 de julho de 2019, a série StorSimple 5000/7000 chegará ao status EOS (fim do suporte). É recomendável que os clientes da série StorSimple 5000/7000 migrem para uma das alternativas descritas no documento.

O StorSimple séries 5000-7000 estará alcançando o [fim do Suporte](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. Os clientes que estão executando o StorSimple séries 5000-7000 têm a opção de atualizar para outros serviços híbridos internos do Azure. Este artigo descreve as opções híbridas do Azure disponíveis para migrar dados. 

## <a name="migration-options"></a>Opções de migração

Os clientes que usam StorSimple séries 5000-7000 têm as opções a seguir:

- **Opções do Azure**:

    - **Atualizar para StorSimple série 8000** – Atualizar para o StorSimple série 8000 e, desse modo, continuar na plataforma do StorSimple.  Esse caminho de atualização exige que os clientes substituam os dispositivos das séries 5000-7000 por uma série 8000. Os dados são migrados do dispositivo das séries 5000-7000 usando a ferramenta de migração. Depois que a migração for concluída com êxito, os dispositivos do StorSimple série 8000 continuarão a camada de dados para o Armazenamento de Blobs do Azure. 

    Para obter mais informações sobre como migrar dados usando um StorSimple série 8000, vá para [Migrar dados do StorSimple série 5000-7000 para um dispositivo série 8000](storsimple-8000-migrate-from-5000-7000.md).

    - **Migrar para Sincronização de Arquivos do Azure** – Essa nova opção de migração permite que os clientes armazenem os compartilhamentos de arquivos da organização nos Arquivos do Azure. Esses compartilhamentos de arquivos são então centralizados para acesso local usando AFS (Sincronização de Arquivos do Azure). A AFS pode ser implantada em um host do Windows Server. A migração de dados real é então executada como uma cópia do host ou usando a ferramenta de migração.

    Para obter mais informações sobre como migrar dados para a Sincronização de Arquivos do Azure, consulte [Migrar dados do StorSimple séries 5000-7000 para Sincronização de Arquivos do Azure](https://aka.ms/StorSimpleMigrationAFS).

- **Opções de terceiros**:

    - **Migrar para o NAS do Panzura Freedom** – clientes do StorSimple 5000-7000 podem optar por migrar para o Panzura Freedom para manter os dados no Azure. A solução Panzura Freedom fornece uma solução NAS que abrange datacenters, escritórios e nuvens públicas e privadas. A solução habilita fluxos de trabalho de dados na nuvem, híbridos e locais para clientes móveis, SMB e NFS. Há suporte para essa migração no Panzura e os clientes podem começar solicitando o suporte à migração do [site do Panzura](https://panzura.com/storsimple-migration/).

## <a name="migration---frequently-asked-questions"></a>Migração - Perguntas frequentes

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>P. Quando os dispositivos do StorSimple séries 5000 e 7000 alcançarão o fim do serviço? 

a. O StorSimple séries 5000-7000 alcançará o [fim do serviço](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. O fim do serviço implica que a Microsoft não poderá mais fornecer suporte para hardware e software desses dispositivos após julho de 2019. É altamente recomendável que você comece a formular um plano para migrar os dados dos dispositivos agora.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>P. O que acontecerá com os dados armazenados no Azure?  

a. Será possível continuar usando os dados no Azure após migrá-los para um serviço mais recente. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>P. O que acontecerá com os dados que tenho armazenados localmente no dispositivo do StorSimple? 

a. Os dados que estão no dispositivo local poderão ser copiados para o serviço mais recente, conforme descrito nos documentos de migração.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>P. O que acontecerá se eu quiser manter o dispositivo do StorSimple séries 5000/7000? 

a. Embora os serviços poderão continuar funcionando, a Microsoft não poderá mais dar suporte de hardware e software. A migração é altamente recomendável para continuidade de negócios.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>P. Quais opções estão disponíveis para migrar dados dos dispositivos das séries 5000-7000 do StorSimple? 

a. Dependendo do cenário, os usuários do StorSimple séries 5000-7000 terão as seguintes opções de migração. 

 - **Atualizar para a série 8000**: use essa opção quando quiser continuar na plataforma do StorSimple. 
 - **Migrar para Sincronização de Arquivos do Azure**: use essa opção quando quiser alternar para o formato nativo do Azure. É possível usar a Sincronização de Arquivos do Azure para gerenciamento centralizado de compartilhamentos de arquivos. 

Contate o Suporte da Microsoft para discutir sobre as opções de migração não listadas aqui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>P. Há suporte de migração para outras soluções de armazenamento?

a. Sim. Há suporte para migração de outras soluções de armazenamento usando a cópia do host dos dados.

### <a name="q-is-migration-supported-by-microsoft"></a>P. A Microsoft dá suporte à migração? 

a. A migração da série 5000 ou 7000 é uma operação totalmente com suporte. Na verdade, a Microsoft recomenda contatar o Suporte antes de iniciar a migração. A migração atualmente é uma operação assistida. Se você pretende migrar dados do dispositivo do StorSimple séries 5000-7000, [Abra um ticket de suporte](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>P. Qual é o modelo de preços para ambas as duas opções de migração?

a. O custo da migração varia dependendo da opção escolhida. Embora a migração seja gratuita, se você decidir atualizar para um StorSimple série 8000, haverá o custo do dispositivo de hardware. 

Da mesma forma, ao usar a Sincronização de Arquivos do Azure, os valores de assinatura do serviço poderão ser aplicados. Em cada caso, os clientes também terão que pagar os custos de armazenamento contínuos. Consulte o seguinte para uma estimativa: 
- [Preço do StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Preço do AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>P.  Quanto tempo demora para concluir uma migração?

a. O tempo para migrar dados depende da quantidade de dados e da opção de atualização selecionada. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>P. Qual é a data de Encerramento do Suporte para a série StorSimple 8000?

a. A data de Encerramento do Suporte para a série StorSimple 8000 é publicada [aqui](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Próximas etapas
 - [Migrar dados de um StorSimple séries 5000-7000 para um dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrar dados de um StorSimple séries 5000-7000 para a Sincronização de Arquivos do Azure](storsimple-5000-7000-afs-migration.md)
