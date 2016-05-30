<properties
   pageTitle="Instalar a Atualização 2.1 no dispositivo StorSimple | Microsoft Azure"
   description="Explica como instalar a Atualização 2.1 do StorSimple série 8000 em seu dispositivo StorSimple série 8000."
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
   ms.date="05/18/2016"
   ms.author="alkohli" />

# Instalar a Atualização 2.1 no dispositivo StorSimple

## Visão geral

Este tutorial explica como instalar a Atualização 2.1 em um dispositivo StorSimple que está executando uma versão de software anterior por meio do Portal Clássico do Azure e usando o método de hotfix. O método de hotfix é usado quando um gateway é configurado em uma interface de rede que não seja DATA 0 do dispositivo StorSimple e quando você está tentando atualizar de uma versão de software anterior à Atualização 1.

A Atualização 2.1 inclui o software do dispositivo, a WMI e as atualizações de iSCSI. Se estiver atualizando de uma versão anterior à Atualização 2, você também precisará aplicar o LSI driver, Spaceport, Storport e as atualizações de firmware de disco. O software do dispositivo e as correções de WMI, iSCSI, LSI driver, Spaceport e Storport são atualizações sem interrupções e podem ser aplicadas por meio do Portal Clássico do Azure. As atualizações de firmware de disco são interruptivas e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo.

> [AZURE.IMPORTANT]

> -  Talvez você não veja a Atualização 2.1 imediatamente porque fazemos uma distribuição das atualizações dividida em fases. Procure novamente as atualizações em poucos dias, uma vez que elas serão disponibilizadas em breve.
> - Um conjunto de verificações prévias manuais e automáticas para são realizadas antes da instalação para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas pré-verificações são executadas somente se você aplicar as atualizações no portal clássico do Azure.
> - Recomendamos que você instale as atualizações de software e driver através do Portal clássico do Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização de gateway falhar no portal. Dependendo da versão da qual você está atualizando, as atualizações podem levar de 1,5 a 2,5 horas para serem instaladas. As atualizações do modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações com interrupção, elas resultarão em um tempo de inatividade para seu dispositivo.
> - Se estiver executando o StorSimple Snapshot Manager opcional, certifique-se de ter atualizado sua versão do Snapshot Manager para a Atualização 2.1 antes de atualizar o dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Instalar a Atualização 2.1 por meio do Portal Clássico do Azure

Realize as etapas a seguir para atualizar seu dispositivo para a [Atualização 2.1](storsimple-update21-release-notes.md).


> [AZURE.NOTE]
Se você estiver aplicando a Atualização 2 ou posterior (incluindo a Atualização 2.1), a Microsoft poderá receber informações adicionais de diagnóstico do dispositivo. Como resultado, quando nossa equipe de operações identifica dispositivos com problemas, estamos mais bem equipados para coletar informações do dispositivo e diagnosticar problemas. Ao aceitar a Atualização 2 ou posterior, você nos permite oferecer esse suporte proativo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verifique se o dispositivo está executando a **Atualização 2.1 do StorSimple 8000 Series (6.3.9600.17704)**. A **Data da última atualização** também deve ser modificada. 

	Se você estiver atualizando de uma versão anterior à Atualização 2, você também verá que as atualizações do modo de manutenção estarão disponíveis (essa mensagem poderá continuar a ser exibida por até 24 horas após a instalação das atualizações).

    As atualizações do modo de manutenção são atualizações interrompidas que resultam em tempo de inatividade do dispositivo e podem ser aplicadas apenas por meio da interface do Windows PowerShell de seu dispositivo. Em alguns casos quando a Atualização 1.2 estiver sendo executada, o firmware de disco poderá já estar atualizado; nesse caso, não é necessário instalar quaisquer atualizações do modo de manutenção.

	Se você estiver atualizando da Atualização 2, seu dispositivo agora deverá estar atualizado. Você pode ignorar as etapas restantes.

13. Baixe as atualizações do modo de manutenção usando as etapas relacionadas em [Para baixar hotfixes](#to-download-hotfixes) a fim de pesquisar e baixar a KB3121899, que instala as atualizações de firmware de disco (as outras atualizações já devem estar instaladas agora).

13. Siga as etapas relacionadas em [instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações do modo de manutenção.

  

## Instalar a Atualização 2.1 como um hotfix

Use este procedimento se a verificação de gateway falhar ao tentar instalar as atualizações por meio do portal clássico do Azure. A verificação falha pois você tem um gateway atribuído a uma interface de rede diferente de DATA 0 e o dispositivo está executando uma versão de software anterior à Atualização 1.

As versões de software que podem ser atualizadas usando o método de hotfix são:

- Atualização 0.1, 0.2, 0.3
- Atualização 1, 1.1, 1.2
- Atualização 2 

> [AZURE.IMPORTANT]
>
> - Se o dispositivo estiver executando a versão de lançamento (GA), contate o [ Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudar na instalação dessa atualização.

O método de hotfix envolve as três etapas a seguir:

- Baixe os hotfixes do Catálogo do Microsoft Update.
- Instale e verifique os hotfixes do modo normal.
- Instale e verifique o hotfix do modo de manutenção (somente ao atualizar o software anterior à Atualização 2).

#### Baixe as atualizações para um dispositivo que executa o software da Atualização 2

Se o dispositivo estiver executando a Atualização 2, você deverá baixar e instalar os hotfixes a seguir na ordem recomendada:

| Classificar | KB | Descrição | Tipo de atualização | Hora da instalação |
|--------|-----------|-------------------------|------------- |-------------|
| 1\. | KB3162954 | Atualização de software | Regular | ~ 45 Min. |
| 2\. | KB3146621 | Pacote iSCSI | Regular | ~ 20 Min. |
| 3\. | KB3103616 | Pacote WMI | Regular | ~ 12 Min. |


#### Baixe as atualizações para um dispositivo que executa um software anterior à Atualização 2

Se o dispositivo estiver executando as versões 0.2, 0.3, 1.0 e 1.1, você deverá baixar e instalar a atualização do LSI driver e do firmware. Se você estiver executando a Atualização 1.2 ou 2, essa atualização já estará instalada.
 
| Classificar | KB | Descrição | Tipo de atualização | Hora da instalação |
|--------|-----------|-------------------------|------------- |-------------|
| 4\. | KB3121900 | Driver LSI e firmware | Regular | ~ 20 Min. |


<br></br> Se o dispositivo estiver executando as versões 0.2, 0.3, 1.0, 1.1 e 1.2, baixe e instale o Spaceport e a correção do Storport. Se você estiver executando a Atualização 2, eles já estarão instalados.

| Classificar | KB | Descrição | Tipo de atualização | Hora da instalação |
|--------|-----------|-------------------------|------------- |-------------|
| 5\. | KB3090322 | Correção do Spaceport </br> Windows Server 2012 R2 | Regular | ~ 20 Min. |
| 6\. | KB3080728 | Correção do Storport </br> Windows Server 2012 R2 | Regular | ~ 20 Min. |



<br></br> Talvez você precise instalar as atualizações de firmware de disco. Você pode verificar se precisa de atualizações de firmware de disco executando o cmdlet `Get-HcsFirmwareVersion`. Se você estiver executando essas versões de firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, você não precisará instalar essas atualizações.


| Classificar | KB | Descrição | Tipo de atualização | Hora da instalação |
|--------|-----------|-------------------------|------------- |-------------|
| 7\. | KB3121899 | Firmware de disco | Manutenção | ~ 30 Min. |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 2.1. É possível usar o portal clássico do Azure para aplicar atualizações subsequentes.
> - Se você estiver atualizando da Atualização 2, o tempo total de instalação será próximo de 1,5 hora.
> - Antes de usar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores de dispositivo estão online e todos os componentes de hardware estão íntegros.

Execute as seguintes etapas para baixar e instalar os hotfixes.

[AZURE.INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## Próximas etapas

Saiba mais sobre a [versão da Atualização 2.1](storsimple-update21-release-notes.md).

<!---HONumber=AcomDC_0518_2016-->