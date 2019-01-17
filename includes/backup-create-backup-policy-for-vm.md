---
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 11/09/2018
ms.author: raynew
ms.openlocfilehash: b589c88e5b5c5991db43a9f3c10003e17094b2e1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057350"
---
## <a name="defining-a-backup-policy"></a>Definindo uma política de backup
Uma política de backup define uma matriz de quando os instantâneos de dados são obtidos e por quanto tempo esses instantâneos são mantidos. Ao definir uma política para fazer backup de uma VM, você pode disparar um trabalho de backup *uma vez por dia*. Quando você cria uma nova política, ela é aplicada ao cofre. A interface da política de backup fica assim:

![Política do backup](./media/backup-create-policy-for-vms/backup-policy.png)

Para criar uma política:

1. Insira um nome para **Nome da política**.
2. Podem ser feitos instantâneos dos dados em intervalos Diários ou Semanais. Use o menu suspenso **Frequência do Backup** para escolher se os instantâneos de dados são feitos Diariamente ou Semanalmente.

   * Se você escolher um intervalo Diário, use o controle destacado para selecionar a hora do dia para o instantâneo. Para alterar a hora, cancele a seleção da hora e selecione a nova hora.

     ![Política de backup diário](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>
   * Se você escolher um intervalo Semanal, use os controles destacados para selecionar o(s) dia(s) da semana e a hora do dia para fazer o instantâneo. No menu do dia, selecione um ou vários dias. No menu de hora, selecione uma hora. Para alterar a hora, cancele a seleção da hora escolhida e selecione a nova hora.

     ![Política de backup semanal](./media/backup-create-policy-for-vms/backup-policy-weekly.png)
3. Por padrão, todas as opções de **Intervalo de Retenção** estão selecionadas. Desmarque qualquer limite do intervalo de retenção que você não deseja usar. Em seguida, especifique os intervalos a serem usados.

    Os intervalos de retenção Mensal e Anual permitem que você especifique os instantâneos com base em um incremento diário ou semanal.

   > [!NOTE]
   >
  - Ao proteger uma VM, um trabalho de backup é executado uma vez por dia. O tempo durante o qual o backup é executado é o mesmo para cada intervalo de retenção.
  - O ponto de recuperação é gerado na data e hora em que o instantâneo de backup é concluído, independentemente de quando o trabalho de backup foi agendado.
    - Ex.: Se a frequência de backup agendada às 23:30h e, devido a algum problema, o instantâneo é concluído às 00:01h, o ponto de recuperação será criado com a próxima data e o horário de 00:01h.
  - No caso de backup mensal, se o backup é definido para ser executado no primeiro dia de cada mês e se o instantâneo é concluído no dia seguinte devido a algum problema, o ponto de recuperação criado para o backup mensal é marcado com o dia seguinte (ou seja, o segundo desse mês).
   >
   >


4. Depois de definir todas as opções para a política, na parte inferior da folha, clique em **Salvar**.

    A nova política será aplicada imediatamente no cofre.
