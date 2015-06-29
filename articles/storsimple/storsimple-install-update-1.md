<properties 
   pageTitle="Instalar a Atualização 1 no dispositivo StorSimple"
   description="Explica como instalar a Atualização 1 do StorSimple série 8000 em seu dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# Instalar a Atualização 1 no dispositivo StorSimple

## Visão geral

Esse tutorial explica como instalar a Atualização 1 em um dispositivo StorSimple que está executando uma versão de software anterior à Atualização 1. O dispositivo pode estar executando o software na versão de disponibilidade geral, Atualização 0.1, Atualização 0.2 ou Atualização 0.3.

Durante a instalação, se o dispositivo estiver executando uma versão anterior à Atualização 1, serão realizadas verificações no dispositivo. Essas verificações determinam a integridade do dispositivo em termos de estado do hardware e conectividade de rede.

Você será solicitado a executar uma pré-verificação manual para garantir que:

- Os IPs fixos do controlador sejam roteáveis e possam se conectar à Internet. Esses IPs são usados para atualizações de serviço no dispositivo StorSimple. Você pode testar isso executando o seguinte cmdlet em cada controlador:

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
	    
	    


- Antes de atualizar o dispositivo, é recomendável capturar um instantâneo da nuvem dos dados do dispositivo.

Depois da verificação e confirmação das verificações manuais (acima), será executado um conjunto de verificações automáticas de pré-atualização. Estão incluídos:

- **Verificações de integridade do controlador** para verificar se ambos os controladores do dispositivo estão em boas condições e online.

- **Verificações de integridade do componente de hardware** para verificar se todos os componentes de hardware no dispositivo StorSimple estão em boas condições.

- **Verificações de DATA 0** para verificar se DATA 0 está habilitada no dispositivo. Se essa interface não estiver habilitada, você precisará habilitá-la e repetir a ação.

- **Verificações de DATA 2 e DATA 3** para verificar se as interfaces de rede DATA 2 e DATA 3 não estão habilitadas. Se essas interfaces estiverem habilitadas, você precisará desabilitá-las e tentar atualizar o dispositivo. Essa verificação será realizada somente se você estiver atualizando de um dispositivo que está executando o software de disponibilidade geral. Os dispositivos que executam versões 0.1, 0.2 ou 0.3 não precisarão dessa verificação.

- **Verificação de gateway** em qualquer dispositivo executando uma versão anterior à Atualização 1. Essa verificação será realizada somente em dispositivos que tenham um gateway configurado para uma interface de rede diferente de DATA 0.
 
A Atualização 1 será aplicada somente se todas as verificações de pré-atualização tiverem sido concluídas com êxito. Depois de ter aplicado a Atualização 1 no dispositivo StorSimple, futuras atualizações não terão as verificações de Data 2 e Data 3 e a verificação de gateway. As outras pré-verificações ocorrerão.

## Usar o Portal de Gerenciamento para instalar a Atualização 1

É recomendável usar o Portal de Gerenciamento do Azure para atualizar um dispositivo que esteja executando a versão de disponibilidade geral. Execute as seguintes etapas para atualizar o dispositivo.

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]


## Solucionando problemas de falhas na atualização

**E se você receber uma notificação informando que as verificações de pré-atualização falharam?**

Se uma pré-verificação falhar, certifique-se de que já observou a barra de notificação detalhada na parte inferior da página. Ela explica os motivos da falha da pré-verificação. A ilustração a seguir mostra uma instância em que uma notificação aparece. Nesse caso, a verificação de integridade do controlador e verificação de integridade do componente de hardware falharam. Na seção **Status de Hardware**, você pode ver que ambos os componentes, o Controlador 0 e o Controlador 1, precisam de atenção.
 
  ![Falha na pré-verificação](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Você precisará ter certeza de que ambos os controladores estejam em boas condições e online. Também será preciso ter certeza de que todos os componentes de hardware no dispositivo StorSimple sejam mostrados como em boas condições na página Manutenção. Em seguida, você pode tentar instalar as atualizações. Se não for possível corrigir os problemas de componente de hardware, você precisará contatar o Suporte da Microsoft para saber das próximas etapas.

**E se você receber uma mensagem de erro “Não foi possível instalar atualizações” e a recomendação for consultar o guia de solução de problemas de atualização para determinar a causa da falha?**

Uma causa provável para isso pode ser a falta de conectividade com os servidores Microsoft Update. Essa é uma verificação manual que precisa ser executada. Se você perder a conectividade com o servidor de atualização, o trabalho de atualização falhará. É possível verificar a conectividade usando o seguinte cmdlet na interface do Windows PowerShell do dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Execute o cmdlet em ambos os controladores.
 
Se você verificou que há conectividade e o problema continuar, contate o Suporte da Microsoft para saber das próximas etapas.

## Próximas etapas

Saiba mais sobre o [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=58_postMigration-->