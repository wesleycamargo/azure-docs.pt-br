---
title: Administração da interface do usuário da Web local do Microsoft Azure Data Box | Microsoft Docs em dados
description: Descreve como usar a interface do usuário da Web local para administrar o dispositivo Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/11/2018
ms.author: alkohli
ms.openlocfilehash: a5f3f1b4d0d67a12ee0ebaa40935b8494e53dab3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956295"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>Usar a interface do usuário da Web local para administrar o Data Box

Este artigo descreve algumas das tarefas de configuração e de gerenciamento que podem ser executadas no Data Box. Você pode gerenciar o Data Box por meio da IU do portal do Azure e da IU da Web local para o dispositivo. Este artigo concentra-se nas tarefas que você pode executar usando a interface do usuário da Web local.

A interface do usuário da Web local do Data Box é usada para a configuração inicial do dispositivo. Você também pode usar a interface do usuário da Web local para desligar ou reiniciar o Data Box, executar testes de diagnóstico, atualizar software, exibir logs de cópia e gerar um pacote de logs para o Suporte da Microsoft.

Este artigo inclui os seguintes tutoriais:

- Gerar um pacote de suporte
- Desligar ou reiniciar seu dispositivo
- Apagar com segurança os dados de seu dispositivo
- Monitorar a capacidade disponível no dispositivo
- Ignorar a validação de soma de verificação 

## <a name="generate-support-package"></a>Gerar pacote de suporte

Se você enfrentar problemas no dispositivo, crie um pacote de suporte com os logs do sistema. O Suporte da Microsoft usará esse pacote para solucionar o problema. Para gerar um pacote de suporte, execute as seguintes etapas:

1. Na interface do usuário da Web local, acesse **Contatar Suporte** e clique em **Criar pacote de suporte**.

    ![Criar pacote de suporte 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Um pacote de suporte será coletado. Essa operação leva alguns minutos.

    ![Criar pacote de suporte 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Após a conclusão da criação do pacote de suporte, clique em **Baixar Pacote de Suporte**. 

    ![Criar pacote de suporte 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Procure e escolha o local de download. Abra a pasta para exibir o conteúdo.

    ![Criar pacote de suporte 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Desligar ou reiniciar seu dispositivo

Você pode desligar ou reiniciar o Data Box usando a interface do usuário da Web local. Nós recomendamos que antes de reiniciar, você coloque os compartilhamentos offline no host e, em seguida, no dispositivo. Isso minimiza a possibilidade de dados corrompidos. Verifique se a cópia de dados não está em andamento quando você desligar o dispositivo.

Para desligar o Data Box, execute as seguintes etapas.

1. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.
2. Clique em **Desligar**.

    ![Desligar o Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Quando a confirmação for solicitada, clique em **OK** para continuar.

    ![Desligar o Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Depois que o dispositivo for desligado, use o botão de energia no painel frontal para ligar o dispositivo.

Para reiniciar o Data Box, execute as seguintes etapas.

1. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.
2. Clique em **Reiniciar**.

    ![Reiniciar o Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Quando a confirmação for solicitada, clique em **OK** para continuar.

   O dispositivo será desligado e, em seguida, reiniciado.

## <a name="view-available-capacity-of-the-device"></a>Exibir a capacidade disponível no dispositivo

Use o painel do dispositivo para exibir a capacidade disponível e é usada do dispositivo. 

1. Na interface do usuário da Web local, acesse **Exibir o painel**.
2. Em **Conectar e copiar**, o espaço livre e usado no dispositivo é mostrado.

    ![Exibir a capacidade disponível](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Ignorar a validação de soma de verificação

A soma de verificação é gerada para os seus dados por padrão, quando você se prepara para o envio. Em alguns casos raros, dependendo do tipo de dados (tamanhos de arquivo pequeno), o desempenho pode ser muito lento. Nesses casos, você pode ignorar a soma de verificação. 

É altamente recomendado que você não desabilite a soma de verificação, a menos que o desempenho seja gravemente afetado.

1. No canto superior direito da interface do usuário da Web local do dispositivo, acesse Configurações.

    ![Desabilitar a soma de verificação](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Desabilitar** a validação de soma de verificação
3. Clique em **Aplicar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar o Data Box pelo portal do Azure](data-box-portal-admin.md).

