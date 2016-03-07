<properties 
   pageTitle="Exibir e gerenciar trabalhos de matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve a página de Trabalhos do serviço StorSimple Manager e como usá-la para controlar trabalhos recentes e atuais para a matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="02/22/2016"
   ms.author="v-sharos" />

# Use o serviço StorSimple Manager para exibir os trabalhos para a matriz de Virtual do StorSimple (visualização)

## Visão geral

A página **Trabalhos** fornece um único portal central para exibir e gerenciar trabalhos iniciados em matrizes Virtuais (também conhecidas como dispositivos virtuais locais) que estão conectadas ao serviço StorSimple Manager. Você pode exibir os trabalhos em execução, concluídos e com falha para vários dispositivos virtuais. Os resultados são apresentados em um formato tabular.

![Página Trabalhos](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Você pode localizar rapidamente os trabalhos nos quais está interessado filtrando os campos, como:

- **Status** – você pode pesquisar todos os trabalhos em execução, concluídos ou com falha.
- **De e Para** – os trabalhos podem ser filtrados com base no intervalo de data e hora.
- **Tipo** – o tipo de trabalho pode ser backup, restauração, failover, baixar atualizações ou instalar atualizações.
- **Dispositivos** – os trabalhos são iniciados em um dispositivo específico conectado ao seu serviço. Os trabalhos filtrados são então tabulados com base nos seguintes atributos:

    - **Tipo** – o tipo de trabalho pode ser backup, restauração, failover, baixar atualizações ou instalar atualizações.

    - **Status** – os trabalhos podem ser em execução, concluído ou com falha.

    - **Entidade** – os trabalhos podem ser associados a um volume, compartilhamento ou dispositivo.

    - **Dispositivo** – o nome do dispositivo no qual o trabalho foi iniciado.

    - **Iniciado em** – a hora em que o trabalho foi iniciado.

    - **Andamento** – o percentual de conclusão de um trabalho em execução. Para um trabalho concluído, sempre deve ser 100%.

A lista de trabalhos é atualizada a cada 30 segundos.

## Exibir detalhes do trabalho

Execute as etapas a seguir para exibir os detalhes de qualquer trabalho.

#### Para exibir detalhes do trabalho

1. Na página **Trabalhos**, exiba o(s) trabalho(s) no(s) qual(is) está interessado executando uma consulta com os filtros apropriados. Você pode pesquisar trabalhos concluídos ou em execução.

2. Selecione um trabalho na lista tabular dos trabalhos.

3. Na parte inferior da página, clique em **Detalhes**.

4. Na caixa de diálogo **Detalhes**, você pode exibir o status, os detalhes e as estatísticas de tempo. A ilustração a seguir mostra um exemplo da caixa de diálogo **Detalhes do trabalho de Backup**.
 
    ![Página de detalhes do trabalho](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### Falhas de trabalho quando a máquina virtual está em pausa no hipervisor

Quando um trabalho estiver em andamento no seu StorSimple Virtual Array e o dispositivo (a máquina virtual provisionada no hipervisor) estiver em pausa há mais de 15 minutos, o trabalho falhará. Isso ocorre devido ao tempo StorSimple Virtual Array estar fora de sincronia com a hora do Microsoft Azure. Um exemplo de uma falha do trabalho de restauração é mostrado na seguinte captura de tela.

![Falha no trabalho de restauração](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Essas falhas serão aplicadas aos trabalhos de backup, restauração, atualização e failover. Se sua máquina virtual for provisionada no Hyper-V, ela eventualmente sincronizará a hora com o hipervisor. Depois que isso acontece, você pode reiniciar seu trabalho.

## Próximas etapas

[Saiba como usar a interface do usuário da Web local para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0224_2016-->