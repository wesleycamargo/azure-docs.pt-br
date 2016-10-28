<properties
   pageTitle="Failover de dispositivo e a recuperação de desastre para sua StorSimple Virtual Array"
   description="Saiba mais sobre como realizar failover em sua StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# Failover de dispositivo e a recuperação de desastre para sua StorSimple Virtual Array


## Visão geral

Este artigo descreve a recuperação de desastre para sua Matriz Virtual Microsoft Azure StorSimple (também conhecido como o dispositivo virtual local StorSimple), incluindo as etapas detalhadas necessárias para fazer failover para outro dispositivo virtual em caso de um desastre. Um failover permitirá que você migre os dados de um dispositivo de *origem* no datacenter para outro dispositivo de *destino* localizado no mesmo local geográfico ou em outro diferente. O failover de dispositivo é para todo o dispositivo. Durante o failover, a propriedade dos dados de nuvem para o dispositivo de origem é alterada para aquela do dispositivo de destino.

O failover de dispositivo é orquestrado por meio do recurso de DR (recuperação de desastre) e é iniciado na página **Dispositivos**. Esta página exibe em formato de tabela todos os dispositivos StorSimple conectados ao seu serviço StorSimple Manager. Para cada dispositivo, o nome amigável, status, capacidade de provisionada e máxima, tipo e modelo são exibidos.

![](./media/storsimple-ova-failover-dr/image15.png)

Este artigo se aplica somente aos StorSimple Virtual Arrays. Para realizar failover em um dispositivo série 8000, vá para [Failover e Recuperação de Desastre do seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).


## O que é recuperação de desastre?

Em um cenário de recuperação de desastre (DR), o dispositivo principal para de funcionar. Nessa situação, você pode mover os dados de nuvem associados ao dispositivo com falha para outro dispositivo por meio do dispositivo principal como a *origem* e especificando outro dispositivo como o *destino*. Esse processo é conhecido como *failover*. Durante o failover, todos os volumes ou compartilhamentos do dispositivo de origem sofrem alteração de propriedade e são transferidos para o dispositivo de destino. Não há permissão para nenhuma filtragem dos dados.

A recuperação de desastres é modelada como uma restauração completa do dispositivo usando a disposição em camadas e acompanhamento baseados em mapa de calor. Um mapa de calor é definido pela atribuição de um valor de calor para os dados com base nos padrões de leitura e gravação. Esse mapa de calor, em seguida, distribui em camadas os fragmentos de dados com nível de calor mais baixo para a nuvem primeiro, mantendo enquanto isso os fragmentos de dados com nível de calor maior alto (mais usados) na camada local. Durante uma recuperação de desastre, o mapa de calor é usado para restaurar e reidratar os dados da nuvem. O dispositivo agrupa todos os volumes/compartilhamentos no último backup recente (conforme determinado internamente) e executa uma restauração desse backup. Todo o processo de recuperação de desastre é coordenado pelo dispositivo.


## Pré-requisitos para failover de dispositivo


### Pré-requisitos

Para qualquer failover de dispositivo, os seguintes pré-requisitos devem ser atendidos:

- O dispositivo de origem deve estar em um estado **Desativado**.

- O dispositivo de destino deve aparecer como **Ativo** no portal clássico do Azure. Você precisará provisionar um dispositivo virtual de destino de capacidade igual ou superior. Em seguida, você deve usar a interface do usuário da Web local para configurar e registrar com êxito o dispositivo virtual.

	> [AZURE.IMPORTANT] Não tente configurar o dispositivo virtual registrado por meio do serviço clicando em **concluir a configuração do dispositivo**. Nenhuma configuração de dispositivo deve ser realizada por meio do serviço.

- O dispositivo de origem e o de destino precisam ser do mesmo tipo. Você só pode realizar failover de um dispositivo virtual configurado como um servidor de arquivos para outro servidor de arquivos. O mesmo é verdadeiro para um servidor iSCSI.

- Para a recuperação de desastre de um servidor de arquivos, recomendamos que você ingresse o dispositivo de destino ao mesmo domínio que o dispositivo de origem, para que as permissões de compartilhamento sejam resolvidas automaticamente. Nesta versão, há suporte apenas para o failover de um dispositivo de destino no mesmo domínio.

### Outras considerações

- Recomendamos que você deixe todos os volumes ou compartilhamentos no dispositivo de origem em estado offline.

- Se é um failover planejado, recomendamos que você faça um backup do dispositivo e, em seguida, continue com o failover para minimizar a perda de dados. Se for um failover não planejado, o backup mais recente será usado para restaurar o dispositivo.

- Os dispositivos de destino disponíveis para recuperação de desastre são dispositivos que têm a capacidade igual ou maior que a do dispositivo de origem. Os dispositivos que estão conectados ao serviço, mas não atendem aos critérios de espaço suficiente não estarão disponíveis como dispositivos de destino.

### Pré-verificações para recuperação de desastre

Antes de iniciar a recuperação de desastre, pré-verificações são executadas no dispositivo. Essas verificações ajudam a garantir que nenhum erro ocorra quando a recuperação de desastres começar. As pré-verificações incluem:

- Validar a conta de armazenamento

- Verificar a conectividade de nuvem do Azure

- Verificar o espaço disponível no dispositivo de destino

- Verificar se um dispositivo de origem do servidor iSCSI tem nomes de ACR válidos, IQN (não excedendo 220 caracteres de comprimento) e a senha CHAP (12 e 16 caracteres de comprimento) associados aos volumes

Se alguma das pré-verificações acima falhar, você não pode continuar com a recuperação de desastre. Você precisa resolver esses problemas e então tentar novamente a recuperação de desastre.

Após a recuperação de desastre ser concluída com êxito, a propriedade dos dados de nuvem no dispositivo de origem é transferida para o dispositivo de destino. O dispositivo de origem, em seguida, não está mais disponível no portal. O acesso a todos os volumes/compartilhamentos no dispositivo de origem é bloqueado e o dispositivo de destino torna-se ativo.

> [AZURE.IMPORTANT]
> 
> Embora o dispositivo não esteja mais disponível, a máquina virtual que você provisionou no sistema host ainda está consumindo recursos. Depois que a recuperação de desastre for concluída com êxito, você poderá excluir esta máquina virtual do seu sistema host.

## Fazer failover para uma matriz virtual

Recomendamos que você tenha outra matriz virtual StorSimple provisionada, configurada por meio da interface do usuário da Web local e registrada com o serviço StorSimple Manager antes de executar este procedimento.


> [AZURE.IMPORTANT]
> 
> - Você não tem permissão para fazer failover de um dispositivo da série StorSimple 8000 para um dispositivo virtual 1200.
> - É possível realizar failover de um dispositivo virtual habilitado para FIPS (Federal Information Processing Standard) implantado no Portal do governo para um dispositivo virtual no Portal clássico do Azure. O inverso também é verdadeiro.

Execute as seguintes etapas para restaurar o dispositivo para um dispositivo virtual do StorSimple de destino.

1. Coloque os volumes/compartilhamento em estado offline no host. Consulte as instruções específicas do sistema operacional no host para colocar os volumes/compartilhamentos no estado offline. Se ainda não estão offline, você precisará colocar todos os volumes/compartilhamentos no dispositivo em estado offline, indo até **Dispositivos > Compartilhamentos** (ou **Dispositivo > Volumes**). Selecione um compartilhamento/volume e clique em **Colocar offline** na parte inferior da página. Quando solicitado a confirmar, clique em **Sim**. Repita esse processo para todos os compartilhamentos/volumes no dispositivo.

2. Na página **Dispositivos**, selecione o dispositivo de origem para o failover e clique em **Desativar**. ![](./media/storsimple-ova-failover-dr/image16.png)

3. Será solicitada a sua confirmação. A desativação do dispositivo é um processo permanente que não pode ser desfeito. Você será lembrado também para colocar seus compartilhamentos/volumes em estado offline no host.

	![](./media/storsimple-ova-failover-dr/image18.png)

3. Após a confirmação, a desativação será iniciada. Após a desativação ser concluída com êxito, você será notificado.

	![](./media/storsimple-ova-failover-dr/image19.png)

4. Na página **Dispositivos**, o estado do dispositivo será agora alterado para **Desativado**.

	![](./media/storsimple-ova-failover-dr/image20.png)

5. Selecione o dispositivo desativado e, na parte inferior da página, clique em **Failover**.

6. No assistente Confirmar failover que é aberto, faça o seguinte:

    1. Na lista suspensa de dispositivos disponíveis, escolha um **Dispositivo de destino.** Somente os dispositivos com capacidade suficiente são exibidos na lista suspensa.

    2. Examine os detalhes associados ao dispositivo de origem, como o nome do dispositivo, a capacidade total e os nomes dos compartilhamentos que serão submetidos a failover.

		![](./media/storsimple-ova-failover-dr/image21.png)

7. Selecione **Eu concordo que o failover é uma operação permanente e após o failover ser concluído com êxito, o dispositivo de origem será excluído**.

8. Clique no ícone de verificação ![](./media/storsimple-ova-failover-dr/image1.png).


9. Um trabalho de failover será iniciado e você será notificado. Clique em **Exibir trabalho** para monitorar o failover.

	![](./media/storsimple-ova-failover-dr/image22.png)

10. Na página **trabalhos**, você verá um trabalho de failover criado para o dispositivo de origem. Este trabalho executa as pré-verificações de recuperação de desastre.

	![](./media/storsimple-ova-failover-dr/image23.png)

 	Após as pré-verificações de recuperação de desastre serem bem-sucedidas, o trabalho de failover gerará trabalhos de restauração para cada volume/compartilhamento que existe em seu dispositivo de origem.

	![](./media/storsimple-ova-failover-dr/image24.png)

11. Após a conclusão do failover, vá para a página **Dispositivos**.

	a. Selecione o dispositivo virtual StorSimple que foi usado como o dispositivo de destino para o processo de failover.

	b. Vá para a página **Compartilhamentos** (ou **Volumes** se for um servidor iSCSI). Todos os compartilhamentos (volumes) do antigo dispositivo devem agora estar listados.
 	
	![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **Vídeo disponível**

Este vídeo demonstra como é possível realizar failover de um dispositivo virtual local StorSimple para outro dispositivo virtual.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## BCDR (recuperação de desastre de continuidade de negócios)

Um cenário de BCDR (recuperação de desastre de continuidade de negócios) ocorre quando todo o datacenter do Azure para de funcionar. Isso pode afetar o serviço StorSimple Manager e os dispositivos StorSimple associados.

Se houver dispositivos StorSimple que foram registrados antes da ocorrência de um desastre, talvez eles precisem ser excluídos. Após o desastre, você pode recriar e configurar esses dispositivos.

## Erros durante a recuperação de desastre

**Falha de conectividade de nuvem durante a recuperação de desastre**

Se a conectividade de nuvem é interrompida depois que começou a recuperação de desastre e antes de concluir a restauração de dispositivo, a recuperação de desastre falhará e você será notificado. O dispositivo de destino que foi usado para recuperação de desastre é então marcado como *inutilizável.* O mesmo dispositivo de destino não pode ser usado então para futuras recuperações de desastre.

**Nenhum dispositivo de destino compatível**

Se os dispositivos de destino não têm espaço suficiente, você verá um erro devido a não haver nenhum dispositivo de destino compatível.

**Falhas de pré-verificações**

Se uma das pré-verificações não for atendida, você verá falhas nas pré-verificações.

## Próximas etapas

Saiba mais sobre como [administrar sua StorSimple Virtual Array usando a interface do usuário da Web local](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->