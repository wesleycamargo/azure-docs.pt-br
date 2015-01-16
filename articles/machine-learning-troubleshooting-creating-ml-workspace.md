<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Guia de Solução de Problemas: Criando e se conectando a um espaço de trabalho de aprendizado de máquina do Azure | Azure" description="Soluções para problemas comuns na criação e conexão a um espaço de trabalho de aprendizado de máquina do Azure " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/25/2014" ms.author="garye" />


#Guia de Solução de Problemas: Criando e se conectando a um espaço de trabalho de aprendizado de máquina do Azure

Este guia oferece soluções para alguns desafios encontrados frequentemente ao configurar espaços de trabalho de ML do Azure.

##Proprietário do espaço de trabalho

Quando você cria um novo espaço de trabalho do Azure ML, o ID que você digitar no campo proprietário do espaço de trabalho deve ser de uma conta válida da Microsoft (anteriormente, Windows Live ID), como john-contoso@live.com ou john-contoso@hotmail.com. Não pode ser uma conta que não seja da Microsoft, como sua conta de email corporativo. Para criar uma conta gratuita da Microsoft, acesse [www.live.com](http://www.live.com). 

##Regiões permitidas

Atualmente, o AM do Azure está disponível para visualização pública na região centro-sul dos EUA. Caso sua assinatura não inclua a região centro-sul dos EUA, o erro "Você não tem assinaturas nas regiões permitidas. Regiões permitidas: Centro-sul dos EUA." 

Para solucionar o problema, selecione "Contatar Suporte da Microsoft" (abaixo) no Portal do Azure e selecione **Faturamento** como o **TIPO DE SUPORTE** para solicitar que essa região seja adicionada à sua assinatura. O AM da Azure acrescentará diversas regiões no decorrer do tempo.

![Contact Microsoft support][screen1]

##Conta de armazenamento
 
O serviço de ML do Azure precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente no Centro-Sul dos Estados Unidos ou criar uma nova conta de armazenamento quando criar o novo espaço de trabalho do ML (se tiver uma cota para criar uma nova conta de armazenamento). Para ver se você pode criar uma nova conta de armazenamento, no Portal do Azure, acesse **Configurações** e depois **Uso**.

![Create workspace][screen2]

Após o novo espaço de trabalho do ML ter sido criado, você pode entrar no Estúdio AM usando a conta da Microsoft especificada como conta do proprietário do espaço de trabalho. Caso ocorra o erro "Espaço de trabalho não encontrado", semelhante à captura de tela abaixo, siga os passos a seguir para solucionar o problema.

![Workspace not found][screen3]

1. Liberar os cookies do navegador.

	Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

	![Internet options][screen4]

	Na guia **Geral**, clique em **Excluir...**

	![General tab][screen5]

	Na caixa de diálogo **Excluir histórico de navegação**, certifique-se de que **Cookies and website data** esteja selecionado e clique em **Excluir**.

	![Delete cookies][screen6]

2. Após os cookies terem sido liberados, reinicie o navegador e acesse [https://studio.azureml.net](https://studio.azureml.net). Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft que você especificou como conta do proprietário do espaço de trabalho.

Nosso objetivo é fazer com que a experiência com o ML do Azure seja a mais simples e tranquila possível. Publique quaisquer comentários ou problemas abaixo ou no [Fórum do AM do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning) para nos ajudar a atendê-lo melhor. 

[screen1]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
