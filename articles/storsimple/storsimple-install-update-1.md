<properties
   pageTitle="Instalar a Atualização 1.2 no dispositivo StorSimple | Microsoft Azure"
   description="Explica como instalar a Atualização 1.2 do StorSimple série 8000 em seu dispositivo StorSimple série 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/22/2016"
   ms.author="alkohli" />

# Instalar a Atualização 1.2 no dispositivo StorSimple

## Visão geral

Este tutorial explica como instalar a Atualização 1.2 em um dispositivo StorSimple que está executando uma versão de software anterior à Atualização 1. O tutorial também aborda as etapas adicionais necessárias para a atualização quando um gateway é configurado em uma interface de rede que não seja DATA 0 do dispositivo StorSimple.

A atualização 1.2 inclui atualizações de software do dispositivo, as atualizações do driver LSI e atualizações de firmware de disco. As atualizações de software e do driver LSI sem interrupções e podem ser aplicadas por meio do Portal clássico do Azure. As atualizações de firmware de disco são as atualizações de interrupção e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo.

Dependendo de qual versão seu dispositivo está executando, você pode determinar se a atualização 1.2 será aplicada. Você pode verificar a versão do software do seu dispositivo navegando até a seção **verificação rápida** no **Painel** do seu dispositivo.

</br>

| Se estiver executando a versão do software... | O que acontece no portal? |
|---------------------------------|--------------------------------------------------------------|
| Versão - GA | Se estiver executando a versão de lançamento (GA), não aplique essa atualização. [Contatar Suporte da Microsoft](storsimple-contact-microsoft-support.md) para atualizar o seu dispositivo.|
| Atualização 0.1 | Portal aplica a atualização 1.2. |
| Atualização 0.2 | Portal aplica a atualização 1.2. |
| Atualização 0.3 | Portal aplica a atualização 1.2. |
| Atualização 1 | Essa atualização não estará disponível. |
| Atualização 1.1 | Essa atualização não estará disponível. |

</br>

> [AZURE.IMPORTANT]

> -  Talvez você não veja a Atualização 1.2 imediatamente porque fazemos uma distribuição em fases das atualizações. Procure novamente as atualizações em poucos dias, uma vez que elas serão disponibilizadas em breve.
> - Esta atualização inclui um conjunto de verificações prévias manuais e automáticas para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas pré-verificações são executadas somente se você aplicar as atualizações no portal clássico do Azure.
> - Recomendamos que você instale as atualizações de software e driver através do Portal clássico do Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização de gateway falhar no portal. As atualizações podem levar de 5 a 10 horas para instalar (incluindo as atualizações do Windows). As atualizações do modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações com interrupção, elas resultarão em um tempo de inatividade para seu dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Instalar a Atualização 1.2 através do portal clássico do Azure

Realize as etapas a seguir para atualizar seu dispositivo para a [Atualização 1.2](storsimple-update1-release-notes.md). Use este procedimento somente se você tiver um gateway configurado na interface de rede DATA 0 no seu dispositivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verifique se o dispositivo está executando a **Atualização 1.2 do StorSimple 8000 Series (6.3.9600.17584)**. A **Data da última atualização** também deve ser modificada. Você também verá que as atualizações do modo de Manutenção estão disponíveis (essa mensagem poderá continuar sendo exibida por até 24 horas após a instalação das atualizações).

    As atualizações do modo de manutenção são atualizações interrompidas que resultam em tempo de inatividade do dispositivo e podem ser aplicadas apenas por meio da interface do Windows PowerShell de seu dispositivo.

    ![Página de manutenção](./media/storsimple-install-update-1/InstallUpdate12_10M.png "Página de manutenção")

13. Baixe as atualizações do modo de manutenção usando as etapas listadas em [Para baixar hotfixes](#to-download-hotfixes) a fim pesquisar e baixar a KB3063416, que instala atualizações de firmware de disco (as outras atualizações já devem estar instaladas agora).

13. Siga as etapas listadas em [Instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações do modo de manutenção.

14. No portal clássico do Azure, navegue até a página **Manutenção** e, na parte inferior da página, clique em **Verificar Atualizações** para verificar se há Atualizações do Windows e clique em **Instalar Atualizações**. O processo estará concluído depois que todas as atualizações forem instaladas com êxito.



## Instalar a Atualização 1.2 em um dispositivo com um gateway configurado em uma interface de rede diferente de DATA 0

Você deve usar este procedimento somente se a verificação de gateway falhar ao tentar instalar as atualizações por meio do portal clássico do Azure. A verificação falha pois você tem um gateway atribuído a uma interface de rede diferente de DATA 0 e o dispositivo está executando uma versão de software anterior à Atualização 1. Se seu dispositivo não tiver um gateway em uma interface de rede DATA 0, você poderá atualizá-lo diretamente no portal clássico do Azure. Consulte [Instalar a Atualização 1.2 através do portal clássico do Azure](#install-update-1.2-via-the-azure-classic-portal).

As versões de software que podem ser atualizadas usando esse método são Atualização 0.1, Atualização 0.2 e Atualização 0.3.


> [AZURE.IMPORTANT]
>
> - Se o dispositivo estiver executando a versão de lançamento (GA), contate o [ Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudar na instalação dessa atualização.
> - Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 1.2. É possível usar o portal clássico do Azure para aplicar atualizações subsequentes.

Se o dispositivo estiver executando o software anterior à Atualização 1 e tiver um gateway definido para uma interface de rede diferente de DATA 0, você poderá aplicar a Atualização 1.2 das duas formas a seguir:

- **Opção 1**: baixe a atualização e aplique-a usando o cmdlet `Start-HcsHotfix` na interface do Windows PowerShell do dispositivo. Esse é o método recomendado. **Não use esse método para aplicar a Atualização 1.2 se o dispositivo estiver executando a Atualização 1.0 Atualização 1.1.**

- **Opção 2**: remova a configuração de gateway e instale a atualização diretamente do portal clássico do Azure.


As instruções detalhadas de cada uma delas são fornecidas nas seções a seguir.

## Opção 1: Usar o Windows PowerShell para StorSimple para aplicar a Atualização 1.2 como um hotfix

Você deve usar este procedimento somente se estiver executando a Atualização 0.1, 0.2, 0.3 e se a verificação de gateway falhou ao tentar instalar atualizações do portal clássico do Azure. Se você estiver executando o software de versão de lançamento (GA), contate o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para atualizar o seu dispositivo.

Para instalar a Atualização 1.2 como um hotfix, é necessário baixar e instalar os seguintes hotfixes:

| Classificar | KB | Descrição | Tipo de atualização |
|--------|-----------|-------------------------|------------- |
| 1 | KB3063418 | Atualização de software | Regular |
| 2 | KB3043005 | Atualização do controlador SAS de LSI | Regular |
| 3 | KB3063416 | Firmware de disco | Manutenção |

Antes de usar este procedimento para aplicar a atualização, verifique se:

- Ambos os controladores de dispositivo estão online.

Execute as etapas a seguir para aplicar a Atualização 1.2. **As atualizações podem levar cerca de 2 horas (cerca de 30 minutos para o software, 30 minutos para driver, 45 minutos para firmware de disco).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## Opção 2: usar o portal clássico do Azure para aplicar a Atualização 1.2 depois de remover a configuração de gateway

Esse procedimento se aplica somente a dispositivos StorSimple que estejam executando uma versão de software anterior à Atualização 1 e tenham um gateway definido em uma interface de rede diferente de DATA 0. Você precisará limpar a configuração do gateway antes de aplicar a atualização.

A atualização pode levar algumas horas para ser concluída. Se seus hosts estiverem em sub-redes diferentes, remover a configuração de gateway nas interfaces iSCSI pode resultar em tempo de inatividade. É recomendável configurar DATA 0 para tráfego iSCSI a fim de reduzir o tempo de inatividade.

Execute as seguintes etapas para desativar a interface de rede com o gateway e, em seguida, aplique a atualização.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## Próximas etapas

Saiba mais sobre a [versão da Atualização 1.2](storsimple-update1-release-notes.md).

<!---HONumber=AcomDC_0824_2016-->