<properties 
   pageTitle="Instalar Atualizações em um StorSimple Virtual Array | Microsoft Azure"
   description="Descreve como usar a IU da Web do StorSimple Virtual Array para aplicar atualizações usando o portal e o método de hotfix"
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# Instalar Atualizações em seu StorSimple Virtual Array

## Visão geral

Este artigo descreve as etapas necessárias para instalar atualizações em seu StorSimple Virtual Array usando a IU da Web local. Esse procedimento leva menos de 2 minutos para ser concluído. É necessário aplicar atualizações de software ou hotfixes para manter seu StorSimple Virtual Array atualizado.

Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Uma vez que o StorSimple Virtual Array é um dispositivo de nó único, qualquer processo de E/S em andamento será interrompido e o dispositivo apresentará tempo de inatividade.

Antes de aplicar uma atualização, é recomendável que você deixe os volumes ou compartilhamentos offline no host primeiro e, em seguida, no dispositivo. Isso minimizará a possibilidade de dados corrompidos.

## Usar a interface do usuário da Web local 
Para instalar a atualização mais recente, você deve usar a IU da Web local para aplicar hotfixes ou atualizações (não é possível usar o portal clássico do Azure para instalar a atualização no momento).

Há duas etapas ao usar a interface do usuário da Web local:

- Baixe a atualização ou o hotfix
- Instale a atualização ou o hotfix

### Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

#### Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Caso esta seja a primeira vez que estiver usando o Catálogo do Microsoft Update neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.
   
	![Instalar o catálogo](./media/storsimple-ova-install-update-01/install-catalog.png)

3. Na caixa de pesquisa do catálogo do Microsoft Update, insira o número da KB (base de dados de conhecimento) do hotfix que deseja baixar. Digite **3160441** para a Atualização 0.2 ou **3160441** para a Atualização 0.1 e clique em **Pesquisar**.

    A listagem de hotfix aparece, por exemplo, como **StorSimple Virtual Array Atualização 0.1**.

    ![Pesquisar o catálogo](./media/storsimple-ova-install-update-01/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao carrinho de compras.

5. Clique em **Exibir carrinho**.

6. Clique em **Download**. Especifique ou **Procure** a localização em que deseja que o download apareça. As atualizações são baixadas para o local especificado e colocadas em uma subpasta com o mesmo nome que a atualização. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.


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

4. Depois que a reinicialização for concluída, você será levado à página de entrada. Você pode verificar a versão do software. Vá para **Manutenção** > **Atualização de Software**. A versão de software exibida deve ser **10.0.10280.0** para a Atualização 0.2 ou **10.0.10279.0** para a Atualização 0.1.

	> [AZURE.NOTE] Relatamos as versões de software de maneira ligeiramente diferente na interface do usuário da Web local e no portal clássico do Azure. Por exemplo, a IU da Web local informa **10.0.0.0.10279** e o portal clássico do Azure informa **10.0.10279.0** para a mesma versão.

	![atualizar dispositivo](./media/storsimple-ova-install-update-01/update9m.png)

## Próximas etapas

Saiba mais sobre a [administração de seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->