<properties 
	pageTitle="Criar um espaço de trabalho de Aprendizado de Máquina | Microsoft Azure" 
	description="Como criar um espaço de trabalho para o Estúdio de Aprendizado de Máquina do Microsoft Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="garye"/>


# Criar um espaço de trabalho de Aprendizado de Máquina do Azure 

Para usar o Estúdio de Aprendizado de Máquina do Azure, você precisa ter um espaço de trabalho de do Aprendizado de Máquina. Esse espaço de trabalho contém as ferramentas necessárias para criar, gerenciar e publicar testes.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Para criar um espaço de trabalho

1. Entre na sua conta do Microsoft Azure.
2. No painel de serviços do Microsoft Azure, clique em **APRENDIZADO DE MÁQUINA**.

    ![Serviço de Aprendizado de Máquina][1]

3. Clique em **+NOVO** na parte inferior da janela.
4. Clique em **SERVIÇOS DE DADOS**, depois em **APRENDIZADO DE MÁQUINA** e, em seguida, **CRIAÇÃO RÁPIDA**.

	![Criação Rápida do novo espaço de trabalho][3]

5. Insira um **NOME DO ESPAÇO DE TRABALHO** para seu espaço de trabalho e especifique o **PROPRIETÁRIO DO ESPAÇO DE TRABALHO**. O proprietário do espaço de trabalho deve ser uma conta da Microsoft válida (por exemplo, name@outlook.com).

    > [AZURE.NOTE]Mais tarde, você pode compartilhar os testes em que está trabalhando convidando outras pessoas para seu espaço de trabalho. Pode fazer isso no Estúdio de Aprendizado de Máquina na página **CONFIGURAÇÕES**. Basta ter a conta da Microsoft ou a conta da empresa de cada usuário.

6. Especificar o **LOCAL** do Azure, depois insira uma **CONTA DE ARMAZENAMENTO** do Azure existente ou selecione **Criar uma nova conta de armazenamento** para criar uma nova.
7. Clique em **CRIAR UM ESPAÇO DE TRABALHO AM**.

Após seu espaço de trabalho do Aprendizado de Máquina ser criado, você o verá listado na página de **aprendizado de máquina**.

Para saber mais sobre como gerenciar seu espaço de trabalho, consulte [Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure]. Se você encontrar um problema ao criar seu espaço de trabalho, consulte [Guia de solução de problemas: criar e conectar-se a um espaço de trabalho do Aprendizado de Máquina].

[Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure]: machine-learning-manage-workspace.md
[Guia de solução de problemas: criar e conectar-se a um espaço de trabalho do Aprendizado de Máquina]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->

<!---HONumber=July15_HO3-->