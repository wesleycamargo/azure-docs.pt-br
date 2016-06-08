<properties 
   pageTitle="Instale a Atualização 0.1 na StorSimple Virtual Array | Microsoft Azure"
   description="Descreve como usar a interface do usuário da Web da StorSimple Virtual Array para aplicar a Atualização 0.1 usando o portal e o método de hotfix"
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
   ms.date="05/24/2016"
   ms.author="alkohli" />

# Instale a Atualização 0.1 em sua StorSimple Virtual Array

## Visão geral

Este artigo descreve as etapas necessárias para instalar a Atualização 0.1 em sua StorSimple Virtual Array. Ele descreve os procedimentos de atualização por meio do portal clássico do Azure e da interface do usuário da Web local. Cada procedimento de atualização leva menos de 2 minutos para ser concluído.

Normalmente, é necessário aplicar atualizações de software ou hotfixes para manter sua StorSimple Virtual Array atualizada. Em geral, é recomendável que você instale atualizações por meio do portal clássico do Azure. No entanto, em casos em que o portal não está disponível, você pode usar a interface do usuário da Web local para aplicar hotfixes ou atualizações.

Tenha em mente que uma instalação de atualização ou hotfix reinicia seu dispositivo. Visto que a StorSimple Virtual Array é um dispositivo de nó único, qualquer E/S andamento será interrompida e o dispositivo apresentará tempo de inatividade.

Antes de aplicar uma atualização, é recomendável que você deixe os volumes ou compartilhamentos offline no host primeiro e, em seguida, no dispositivo. Isso minimizará a possibilidade de dados corrompidos.

## Use o portal clássico do Azure

É recomendável que você instale atualizações por meio do portal clássico do Azure. O procedimento do portal requer que o usuário verifique, baixe e instale as atualizações. Execute as etapas a seguir para instalar a atualização ou o hotfix.

#### Para instalar atualizações por meio do portal clássico do Azure

1. Na página **Dispositivos** selecione o dispositivo no qual você deseja instalar as atualizações.

2. Navegue até **Dispositivos** > **Manutenção** > **Atualizações de Software**.

3. Você verá uma mensagem se as atualizações de software estiverem disponíveis. Para verificar se há atualizações, também é possível clicar em **Verificar Atualizações** na parte inferior da página.

	![Verificar atualizações](./media/storsimple-ova-install-update-01/aupdate1m.png)

4. No final da página, clique em **Baixar atualizações**. Uma caixa de diálogo notifica o usuário que a atualização é com interrupção. Dado que a StorSimple Virtual Array é um dispositivo de nó único, o dispositivo será reiniciado após ser atualizado. Isso interromperá qualquer E/S em andamento. Clique no ícone de seleção para iniciar um trabalho para baixar as atualizações disponíveis.

	![Confirmar atualizações do download](./media/storsimple-ova-install-update-01/aupdate3m.png)

	Você será notificado após as atualizações serem baixadas.

	![Baixar atualizações](./media/storsimple-ova-install-update-01/aupdate5m.png)

5. Na parte inferior da página, clique em **Instalar atualizações** para começar a atualização do dispositivo. A caixa de diálogo será exibida novamente. Clique no ícone de seleção para iniciar um trabalho para instalar as atualizações.
 
 	![Confirmar atualizações de instalação](./media/storsimple-ova-install-update-01/aupdate6m.png)
 
 
	Você será notificado depois que o trabalho for criado.

	![Instalar atualizações](./media/storsimple-ova-install-update-01/aupdate8m.png)
	
6. Clique no link **Exibir trabalho** link para ir para a página **Trabalhos** e monitorar o status da instalação. Você pode clicar em **Detalhes** a qualquer momento para obter informações detalhadas sobre o trabalho de atualização.

	![Monitorar atualizações](./media/storsimple-ova-install-update-01/aupdate9m.png)

6. Após a instalação ser concluída (conforme indicado pelo status do trabalho em 100%), vá para **Dispositivos** > **Manutenção** > **Atualizações de software**. A versão de software exibido deve ser **10.0.10279.0**.

	![Verificar atualizações](./media/storsimple-ova-install-update-01/aupdate13m.png)

## Usar a interface do usuário da Web local 

Use a interface do usuário da Web local para aplicar atualizações quando o portal clássico do Azure não estiver disponível. Há duas etapas ao usar a interface do usuário da Web local:

- Baixe a atualização ou o hotfix
- Instale a atualização ou o hotfix

### Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

#### Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Caso esta seja a primeira vez que estiver usando o Catálogo do Microsoft Update neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.
   
	![Instalar o catálogo](./media/storsimple-ova-install-update-01/install-catalog.png)

3. Na caixa de pesquisa do catálogo do Microsoft Update, insira o número da KB (base de dados de conhecimento) do hotfix que deseja baixar, por exemplo **3160441**, e clique em **Pesquisar**.

    A listagem de hotfix aparece, por exemplo, **Microsoft Azure StorSimple Virtual Array atualização 0.1**.

    ![Pesquisar o catálogo](./media/storsimple-ova-install-update-01/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao carrinho de compras.

5. Clique em **Exibir carrinho**.

6. Clique em **Download**. Especifique ou **Procure** o local em que deseja que o download apareça. As atualizações são baixadas para o local especificado e colocadas em uma subpasta com o mesmo nome que a atualização. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.


### Instale a atualização ou o hotfix

Antes da instalação da atualização ou hotfix, certifique-se de que você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede. Execute as etapas a seguir para instalar a atualização ou o hotfix.

#### Para instalar a atualização ou hotfix

1. Na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**.

2. Em **Caminho de arquivo de atualização**, digite o nome de arquivo para a atualização ou hotfix. Você também pode navegar até o arquivo de instalação de hotfix ou atualização se colocado em um compartilhamento de rede. Clique em **Aplicar**.

	![atualizar dispositivo](./media/storsimple-ova-install-update-01/update1m.png)

3.  Será exibido um aviso. Dado que esse é um dispositivo de nó único, depois que a atualização for aplicada, o dispositivo será reiniciado e haverá tempo de inatividade. Clique no ícone de verificação.

	![atualizar dispositivo](./media/storsimple-ova-install-update-01/update4m.png)

4. A atualização será iniciada. Depois que o dispositivo for atualizado com êxito, ele será reiniciado. A interface do usuário local não estará acessível nessa duração.

    ![atualizar dispositivo](./media/storsimple-ova-install-update-01/update6m.png)

4. Depois que a reinicialização for concluída, você será levado à página de entrada. Você pode verificar a versão do software. Vá para **Manutenção** > **Atualização de software**. O número de build deve ser **10.0.0.0.10279**.

	> [AZURE.NOTE] Relatamos as versões de software de maneira ligeiramente diferente na interface do usuário da Web local e no portal clássico do Azure. A interface do usuário da Web local relata **10.0.0.0.10279** e o portal clássico do Azure relata **10.0.10279.0** para a mesma versão.

	![atualizar dispositivo](./media/storsimple-ova-install-update-01/update9m.png)

## Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0525_2016-->