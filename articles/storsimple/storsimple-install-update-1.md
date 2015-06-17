<properties 
   pageTitle="Instalar a Atualização 1 no dispositivo StorSimple"
   description="Explica como instalar a Atualização 1 do StorSimple série 8000 em seu dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/27/2015"
   ms.author="v-sharos" />

# Instalar a Atualização 1 no dispositivo StorSimple

## Visão geral

Esse tutorial explica como instalar a Atualização 1 em um dispositivo StorSimple que está executando uma versão de software anterior à Atualização 1. O dispositivo pode estar executando o software na versão de disponibilidade geral, Atualização 0.1, Atualização 0.2 ou Atualização 0.3. O tutorial também explica o que fazer se um gateway for configurado em uma interface de rede que não seja DATA 0 no dispositivo StorSimple.

Durante a instalação, se o dispositivo estiver executando uma versão anterior à Atualização 1.0, serão realizadas verificações no dispositivo. Essas verificações determinam a integridade do dispositivo em termos de estado do hardware e conectividade de rede.

Você será solicitado a executar uma pré-verificação manual para garantir que:

- Os IPs fixos do controlador sejam roteáveis e possam se conectar à Internet. Esses IPs são usados para atualizações de serviço no dispositivo StorSimple. Você pode testar isso executando o seguinte cmdlet em cada controlador:

    `Test-Connection -Source <fixed IP of your device controller> <Destination IP> `
 
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

## Instalar a Atualização 1 em um dispositivo com um gateway em uma interface de rede diferente de DATA 0 

Esse procedimento se aplica a dispositivos StorSimple que estejam executando uma versão de software anterior à Atualização 1.0 e tenham um gateway definido em uma interface de rede diferente de DATA 0.
 
Se seu dispositivo não tiver um gateway em uma interface de rede DATA 0, você poderá atualizá-lo diretamente no Portal de Gerenciamento. Consulte [Usar o Portal de Gerenciamento para instalar a Atualização 1](#use-the-management-portal-to-install-update-1).
 
> [AZURE.NOTE]Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 1.0. É possível usar o Portal de Gerenciamento do Azure para aplicar atualizações subsequentes.
 
Se o dispositivo estiver executando o software anterior à Atualização 1.0 e tiver um gateway definido para uma interface de rede diferente de DATA 0, você poderá aplicar a Atualização 1.0 destas duas formas a seguir:

- **Opção 1**: baixe a atualização e aplique-a usando o cmdlet [Start-HcsHotfix](https://technet.microsoft.com/library/dn688134.aspx) na interface do Windows PowerShell do dispositivo. Esse é o método recomendado.

- **Opção 2**: instale a atualização diretamente do Portal de Gerenciamento.
 
As instruções detalhadas de cada uma delas são fornecidas nas seções a seguir.

### Opção 1: Usar o Windows PowerShell de StorSimple para aplicar a Atualização 1

Antes de usar este procedimento para aplicar a atualização, verifique se:

- Ambos os controladores de dispositivo estão online.

- DATA 2 e DATA 3 estão desabilitadas. Você precisará fazer isso apenas se os dispositivos estiverem executando a versão de disponibilidade geral. Os dispositivos executando a Atualização 0.2 e 0.3 não exigem que elas sejam desabilitadas. Depois que a atualização estiver concluída, você poderá habilitar as interfaces de rede novamente.
 
Execute as etapas a seguir para aplicar a Atualização 1.0. É possível que a atualização leve algumas horas para ser concluída.

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]

### Opção 2: Usar o Portal de Gerenciamento do Azure para aplicar a Atualização 1

A atualização pode levar algumas horas para ser concluída. Se seus hosts estiverem em sub-redes diferentes, remover a configuração de gateway nas interfaces iSCSI pode resultar em tempo de inatividade. É recomendável configurar DATA 0 para tráfego iSCSI a fim de reduzir o tempo de inatividade.
 
Execute as etapas a seguir para limpar a configuração do gateway e aplicar a atualização.
 
[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

## Solucionando problemas de falhas na atualização

**E se você receber uma notificação informando que as verificações de pré-atualização falharam?**

Se uma pré-verificação falhar, certifique-se de que já observou a barra de notificação detalhada na parte inferior da página. Ela explica os motivos da falha da pré-verificação. A ilustração a seguir mostra uma instância em que uma notificação aparece. Nesse caso, a verificação de integridade do controlador e verificação de integridade do componente de hardware falharam. Na seção **Status de Hardware**, você pode ver que ambos os componentes, o Controlador 0 e o Controlador 1, precisam de atenção.
 
  ![Falha na pré-verificação](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Você precisará ter certeza de que ambos os controladores estejam em boas condições e online. Também será preciso ter certeza de que todos os componentes de hardware no dispositivo StorSimple sejam mostrados como em boas condições na página Manutenção. Em seguida, você pode tentar instalar as atualizações. Se não for possível corrigir os problemas de componente de hardware, você precisará contatar o Suporte da Microsoft para saber das próximas etapas.

**E se você receber uma mensagem de erro “Não foi possível instalar atualizações” e a recomendação for consultar o guia de solução de problemas de atualização para determinar a causa da falha?**

Uma causa provável para isso pode ser a falta de conectividade com os servidores Microsoft Update. Essa é uma verificação manual que precisa ser executada. Se você perder a conectividade com o servidor de atualização, o trabalho de atualização falhará. É possível verificar a conectividade usando o seguinte cmdlet na interface do Windows PowerShell do dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> <Destination IP>`

Execute o cmdlet em ambos os controladores.
 
Se você verificou que há conectividade e o problema continuar, contate o Suporte da Microsoft para saber das próximas etapas.

## Próximas etapas

Saiba mais sobre o [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=58--> 