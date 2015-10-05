<properties 
   pageTitle="Instalar a Atualização 1.2 no dispositivo StorSimple | Microsoft Azure"
   description="Explica como instalar a Atualização 1.2 do StorSimple série 8000 em seu dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/17/2015"
   ms.author="alkohli" />

# Instalar a Atualização 1.2 no dispositivo StorSimple

## Visão geral

Este tutorial explica como instalar a Atualização 1.2 em um dispositivo StorSimple que está executando uma versão de software anterior à Atualização 1. O tutorial também aborda as etapas adicionais necessárias para a atualização quando um gateway é configurado em uma interface de rede que não seja DATA 0 do dispositivo StorSimple.

A atualização 1.2 inclui atualizações de software do dispositivo, as atualizações do driver LSI e atualizações de firmware de disco. As atualizações de software e do driver LSI sem interrupções e podem ser aplicadas por meio do Portal de Gerenciamento. As atualizações de firmware de disco são as atualizações de interrupção e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo.

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
> - Esta atualização inclui um conjunto de verificações prévias manuais e automáticas para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas pré-verificações são executadas somente se você aplicar as atualizações no portal do Azure. 
> - Recomendamos que você instale as atualizações de software e driver através do Portal de Gerenciamento do Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização de gateway falhar no Portal. As atualizações podem levar de 5 a 10 horas para instalar (incluindo as atualizações do Windows). As atualizações do modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações com interrupção, elas resultarão em um tempo de inatividade para seu dispositivo.

## Preparar para atualizações
Você precisará executar as seguintes etapas antes de examinar e aplicar a atualização:


1. Tirar um instantâneo dos dados do dispositivo na nuvem.


1. Verifique se os IPs fixos do controlador são roteáveis e podem se conectar à Internet. Esses IPs fixo serão usado para atender às atualizações para o seu dispositivo. Você pode testar isso executando o seguinte cmdlet em cada controlador de interface do Windows PowerShell do dispositivo:

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

## Instale a Atualização 1.2 através do Portal de Gerenciamento 

Use este procedimento somente se você tiver um gateway configurado na interface de rede DATA 0 no seu dispositivo. Execute as seguintes etapas para atualizar o dispositivo.

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]

## Instalar a Atualização 1.2 em um dispositivo com um gateway configurado em uma interface de rede diferente de DATA 0 

Você deve usar este procedimento somente se a verificação de gateway falhar ao tentar instalar as atualizações por meio do Portal de Gerenciamento. A verificação falha pois você tem um gateway atribuído a uma interface de rede diferente de DATA 0 e o dispositivo está executando uma versão de software anterior à Atualização 1. Se seu dispositivo não tiver um gateway em uma interface de rede DATA 0, você poderá atualizá-lo diretamente no Portal de Gerenciamento. Consulte [Usar o Portal de Gerenciamento para instalar a Atualização 1](#install-update-12-via-the-management-portal).

As versões de software que podem ser atualizadas usando esse método são Atualização 0.1, Atualização 0.2 e Atualização 0.3.


> [AZURE.IMPORTANT]
> 
> - Se o dispositivo estiver executando a versão de lançamento (GA), contate o [ Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudar na instalação dessa atualização.
> - Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 1.2. É possível usar o Portal de Gerenciamento do Azure para aplicar atualizações subsequentes.

Se o dispositivo estiver executando o software anterior à Atualização 1 e tiver um gateway definido para uma interface de rede diferente de DATA 0, você poderá aplicar a Atualização 1.2 das duas formas a seguir:

- **Opção 1**: baixe a atualização e aplique-a usando o cmdlet `Start-HcsHotfix` na interface do Windows PowerShell do dispositivo. Esse é o método recomendado. **Não use esse método para aplicar a Atualização 1.2 se o dispositivo estiver executando a Atualização 1.0 Atualização 1.1.** 

- **Opção 2**: remova a configuração de gateway e instale a atualização diretamente do Portal de Gerenciamento.


As instruções detalhadas de cada uma delas são fornecidas nas seções a seguir.

## Opção 1: Usar o Windows PowerShell para StorSimple para aplicar a Atualização 1.2 como um hotfix

Você deve usar este procedimento somente se você estiver executando a Atualização 0.1, 0.2, 0.3 e se a verificação de gateway falhou ao tentar instalar atualizações do Portal de Gerenciamento. Se você estiver executando o software de versão de lançamento (GA), contate o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para atualizar o seu dispositivo.

Antes de usar este procedimento para aplicar a atualização, verifique se:

- Ambos os controladores de dispositivo estão online.

Execute as etapas a seguir para aplicar a Atualização 1.2. **As atualizações podem levar cerca de 2 horas (cerca de 30 minutos para o software, 30 minutos para driver, 45 minutos para firmware de disco).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## Opção 2: usar o Portal do Azure para aplicar a Atualização 1.2 depois de remover a configuração de gateway

Esse procedimento se aplica somente a dispositivos StorSimple que estejam executando uma versão de software anterior à Atualização 1 e tenham um gateway definido em uma interface de rede diferente de DATA 0. Você precisará limpar a configuração do gateway antes de aplicar a atualização.
 
A atualização pode levar algumas horas para ser concluída. Se seus hosts estiverem em sub-redes diferentes, remover a configuração de gateway nas interfaces iSCSI pode resultar em tempo de inatividade. É recomendável configurar DATA 0 para tráfego iSCSI a fim de reduzir o tempo de inatividade.
 
Execute as seguintes etapas para desativar a interface de rede com o gateway e, em seguida, aplique a atualização.
 
[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

## Solucionando problemas de falhas na atualização

**E se você receber uma notificação informando que as verificações de pré-atualização falharam?**

Se uma pré-verificação falhar, certifique-se de que já observou a barra de notificação detalhada na parte inferior da página. Ela explica os motivos da falha da pré-verificação. A ilustração a seguir mostra uma instância em que uma notificação aparece. Nesse caso, a verificação de integridade do controlador e verificação de integridade do componente de hardware falharam. Na seção **Status de Hardware**, você pode ver que ambos os componentes, o **Controlador 0** e o **Controlador 1**, precisam de atenção.
 
  ![Falha na pré-verificação](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Você precisará ter certeza de que ambos os controladores estejam em boas condições e online. Também será preciso ter certeza de que todos os componentes de hardware no dispositivo StorSimple sejam mostrados como em boas condições na página Manutenção. Em seguida, você pode tentar instalar as atualizações. Se não for possível corrigir os problemas de componente de hardware, você precisará contatar o Suporte da Microsoft para saber das próximas etapas.

**E se você receber uma mensagem de erro “Não foi possível instalar atualizações” e a recomendação for consultar o guia de solução de problemas de atualização para determinar a causa da falha?**

Uma causa provável para isso pode ser a falta de conectividade com os servidores Microsoft Update. Essa é uma verificação manual que precisa ser executada. Se você perder a conectividade com o servidor de atualização, o trabalho de atualização falhará. É possível verificar a conectividade usando o seguinte cmdlet na interface do Windows PowerShell do dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Execute o cmdlet em ambos os controladores.
 
Se você verificou que há conectividade e o problema continuar, contate o Suporte da Microsoft para saber das próximas etapas.


## Próximas etapas

Saiba mais sobre a [versão da Atualização 1.2](storsimple-update1-release-notes.md)

<!---HONumber=Sept15_HO4-->