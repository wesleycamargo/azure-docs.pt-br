<properties 
   pageTitle="Instalar a Atualização 2 no dispositivo StorSimple | Microsoft Azure"
   description="Explica como instalar a Atualização 2 do StorSimple série 8000 em seu dispositivo StorSimple série 8000."
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
   ms.date="01/12/2016"
   ms.author="alkohli" />

# Instalar a Atualização 2 no dispositivo StorSimple

## Visão geral

Este tutorial explica como instalar a Atualização 2 em um dispositivo StorSimple que está executando uma versão de software anterior por meio do portal clássico do Azure. O tutorial também aborda as etapas necessárias para a atualização quando um gateway é configurado em uma interface de rede que não seja DATA 0 do dispositivo StorSimple e você está tentando atualizar de uma versão de software anterior à Atualização 1.

A Atualização 2 inclui atualizações de software do dispositivo, atualizações do driver LSI e atualizações de firmware de disco. As atualizações de software do dispositivo e do LSI são atualizações sem interrupções e podem ser aplicadas por meio do portal clássico do Azure. As atualizações de firmware de disco são as atualizações de interrupção e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo.

> [AZURE.IMPORTANT]
 
> -  Talvez você não veja a Atualização 2 imediatamente porque fazemos uma distribuição das atualizações dividida em fases. Procure novamente as atualizações em poucos dias, uma vez que elas serão disponibilizadas em breve.
> - Um conjunto de verificações prévias manuais e automáticas para são realizadas antes da instalação para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas pré-verificações são executadas somente se você aplicar as atualizações no portal clássico do Azure. 
> - Recomendamos que você instale as atualizações de software e driver através do Portal clássico do Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização de gateway falhar no portal. As atualizações podem levar de 4 a 7 horas para instalar (incluindo as Atualizações do Windows). As atualizações do modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações com interrupção, elas resultarão em um tempo de inatividade para seu dispositivo.
> - Se estiver executando o StorSimple Snapshot Manager opcional, certifique-se de ter atualizado sua versão Snapshot Manager para a Atualização 2 antes de atualizar o dispositivo.

## Preparar para atualizações
Você precisará executar as seguintes etapas antes de examinar e aplicar a atualização:


1. Tirar um instantâneo dos dados do dispositivo na nuvem.

2. Verifique se os IPs fixos do controlador são roteáveis e podem se conectar à Internet. Esses IPs fixo serão usado para atender às atualizações para o seu dispositivo. Você pode testar isso executando o seguinte cmdlet em cada controlador de interface do Windows PowerShell do dispositivo:

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**Saída de amostra para testar conexão quando IPs fixos podem se conectar à Internet**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

Depois que essas pré-verificações manuais foi concluído com êxito, você poderá verificar e instalar as atualizações.

## Instalar a Atualização 2 por meio do portal clássico do Azure 

Este é o procedimento recomendado para atualizar o dispositivo. Execute as seguintes etapas.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## Instalar a Atualização 2 como um hotfix 

Use este procedimento somente se a verificação de gateway falhar ao tentar instalar as atualizações por meio do portal clássico do Azure. A verificação falha pois você tem um gateway atribuído a uma interface de rede diferente de DATA 0 e o dispositivo está executando uma versão de software anterior à Atualização 1.

As versões de software que podem ser atualizadas usando o método de hotfix são Atualização 0.1, Atualização 0.2, Atualização 0.3, Atualização 1, Atualização 1.1 e Atualização 1.2. O método de hotfix envolve as três etapas a seguir:

- Baixe os hotfixes do Catálogo do Microsoft Update.
- Instale e verifique os hotfixes do modo normal.
- Instale e verifique o hotfix do modo de manutenção.

Os hotfixes aplicados por meio desse método são como na tabela abaixo:

| Classificar | KB | Nome | Descrição do pacote | Tipo de atualização |
|--------|-----------|-------------------------|-----------------------------|-------------|
| 1 | KB3121901 | Atualização de software | HcsMdsSfotwareUpdate.exe <br></br> CisMdsAgentUpdateBundle.exe | Regular |
| 2 | KB3121900 | Driver LSI | HcsLsiUpdate.exe | Regular |
| 3 | KB3080728 | Correção do Storport | Storport-KB3080728-x64.msu | Regular |
| 4 | KB3090322 | Correção do Spaceport | Spaceport-KB3090322-x64.msu | Regular |
| 5 | KB3121899 | Firmware de disco | DiskFirmwarePackage.exe | Manutenção |


> [AZURE.IMPORTANT]
> 
> - Se o dispositivo estiver executando a versão de lançamento (GA), contate o [ Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudar na instalação dessa atualização.
> - Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 2. É possível usar o portal clássico do Azure para aplicar atualizações subsequentes.
> - Cada instalação de hotfix pode levar cerca de 20 minutos para concluir. O tempo total de instalação é de quase 2 horas. 
> - Antes de usar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores de dispositivo estão online e todos os componentes de hardware estão íntegros.

Execute as etapas a seguir para aplicar a Atualização 2 como um hotfix.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## Solucionando problemas de falhas na atualização

**E se você receber uma notificação informando que as verificações de pré-atualização falharam?**

Se uma pré-verificação falhar, certifique-se de que já observou a barra de notificação detalhada na parte inferior da página. Ela explica os motivos da falha da pré-verificação. A ilustração a seguir mostra uma instância em que uma notificação aparece. Nesse caso, a verificação de integridade do controlador e verificação de integridade do componente de hardware falharam. Na seção **Status de Hardware**, você pode ver que ambos os componentes, o **Controlador 0** e o **Controlador 1**, precisam de atenção.
 
  ![Falha na pré-verificação](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

Você precisará ter certeza de que ambos os controladores estejam em boas condições e online. Também será preciso ter certeza de que todos os componentes de hardware no dispositivo StorSimple sejam mostrados como em boas condições na página Manutenção. Em seguida, você pode tentar instalar as atualizações. Se não for possível corrigir os problemas de componente de hardware, você precisará contatar o Suporte da Microsoft para saber das próximas etapas.

**E se você receber uma mensagem de erro “Não foi possível instalar atualizações” e a recomendação for consultar o guia de solução de problemas de atualização para determinar a causa da falha?**

Uma causa provável para isso pode ser a falta de conectividade com os servidores Microsoft Update. Essa é uma verificação manual que precisa ser executada. Se você perder a conectividade com o servidor de atualização, o trabalho de atualização falhará. É possível verificar a conectividade usando o seguinte cmdlet na interface do Windows PowerShell do dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Execute o cmdlet em ambos os controladores.
 
Se você verificou que há conectividade e o problema continuar, contate o Suporte da Microsoft para saber das próximas etapas.


## Próximas etapas

Saiba mais sobre a [versão da Atualização 2](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0114_2016-->