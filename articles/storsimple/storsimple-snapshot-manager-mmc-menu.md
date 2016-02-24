<properties 
   pageTitle="Ações de menu do MMC do StorSimple Snapshot Manager | Microsoft Azure"
   description="Descreve como usar as ações de menu padrão do Console de Gerenciamento Microsoft (MMC) no StorSimple Snapshot Manager."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/28/2015"
   ms.author="v-sharos" />

# Usar as ações de menu do MMC no StorSimple Snapshot Manager

## Visão geral

No StorSimple Snapshot Manager, você verá as seguintes ações listadas em todos os menus de ação e todas as variações do painel de **Ações**.

- Visualizar
- Nova Janela a Partir Daqui 
- Atualizar 
- Exportar Lista 
- Ajuda 

Essas ações fazem parte do MMC (Console de Gerenciamento Microsoft) e não são específicas ao StorSimple Snapshot Manager.

Este tutorial descreve essas ações e explica como usar cada uma delas no StorSimple Snapshot Manager.

## Visualizar

Você pode usar a opção **Exibição** para alterar a exibição do painel **Resultados** e alterar o modo de exibição da janela do console.

#### Para alterar o modo de exibição de painel de Resultados

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.

2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique em um item do painel **Resultados** e clique na opção **Exibição**.

3. Para adicionar ou remover as colunas que aparecem no painel **Resultados**, clique em **Adicionar/Remover Colunas**. A caixa de diálogo **Adicionar/Remover Colunas** é exibida.

    ![Adicionar ou remover colunas do painel de Resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png)

4. Preencha o formulário da seguinte maneira:

    - Selecione itens da lista de colunas **Disponível** e clique em **Adicionar** para adicioná-los à **Colunas exibidas**. 

    - Clique nos itens na lista **Colunas exibidas** e clique em **Remover** para removê-los da lista.

    - Selecione um item na lista de colunas **Exibidas** e clique em **Mover para cima** ou **Mover para baixo** para mover o item para cima ou para baixo na lista.

    - Clique em **Restaurar Padrões** para retornar para a configuração padrão do painel **Resultados**.

5. Quando tiver concluído suas seleções, clique em **OK**.

#### Para alterar a exibição da janela de console

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.

2. No painel **Escopo**, clique com o botão direito em qualquer nó, clique em **Exibição** e, depois, clique em **Personalizar**. A caixa de diálogo **Personalizar** é exibida.

    ![Personalizar a janela do console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png)

3. Marque ou desmarque as caixas de seleção para exibir ou ocultar itens na janela do console. Quando tiver concluído suas seleções, clique em **OK**.

## Nova Janela a Partir Daqui

Você pode usar a opção **Nova Janela a Partir Daqui** para abrir uma nova janela do console.

#### Para abrir uma nova janela do console

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.

2. No painel **Escopo**, clique com o botão direito em qualquer nó e clique em **Nova Janela a Partir Daqui**.

    Uma nova janela aparece, mostrando apenas o escopo que você selecionou. Por exemplo, se você clicar com o botão direito no nó **Políticas de Backup**, a nova janela mostrará apenas o nó **Políticas de Backup** no painel **Escopo** e uma lista de políticas de backup definidas no painel **Resultados**. Veja os exemplos a seguir.

    ![Nova Janela a Partir Daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png)
 
## Atualizar

Você pode usar a ação **Atualizar** para atualizar a janela do console.

#### Para atualizar a janela do console

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.

2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique com o botão direito em um item do painel **Resultados** e clique em **Atualizar**.

## Exportar Lista

Você pode usar a ação **Exportar Lista** para salvar uma lista em um arquivo CSV (valores separados por vírgulas). Por exemplo, você pode exportar a lista de políticas de backup ou o catálogo de backups. Em seguida, você pode importar o arquivo CSV em um aplicativo de planilha para análise.

#### Para salvar uma lista em um arquivo CSV (valores separados por vírgulas)

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager. 

2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique com o botão direito em um item do painel de **Resultados** e clique em **Exportar Lista**.

3. A caixa de diálogo **Exportar Lista** é exibida. Preencha o formulário da seguinte maneira:

    1. Na caixa **Nome de arquivo**, digite um nome para o arquivo CSV ou clique na seta para selecionar na lista suspensa.

    2. Na caixa **Salvar como tipo**, clique na seta e selecione um tipo de arquivo na lista suspensa.

    3. Para salvar apenas os itens selecionados, selecione as linhas e marque a caixa de seleção **Salvar apenas linhas selecionadas**. Para salvar todas as listas exportadas, desmarque a caixa de seleção **Salvar apenas linhas selecionadas**.

    4. Clique em **Salvar**.

    ![Exportar lista como um arquivo de valores separados por vírgula](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png)
 
## Ajuda

Você pode usar o menu **Ajuda** para exibir a ajuda online disponível para o MMC e o StorSimple Snapshot Manager.

#### Para exibir a ajuda online disponível

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.

2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique com o botão direito em um item do painel **Resultados** e clique em **Ajuda**.

## Próximas etapas

- Saiba mais sobre [a interface do usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
- Saiba mais sobre [como usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).

<!---HONumber=AcomDC_0107_2016-->