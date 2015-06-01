<properties 
	pageTitle="Habilitar o registro em log de serviços Web de Aprendizado de Máquina | Azure" 
	description="Saiba como habilitar o registro em log de serviços Web de Aprendizado de Máquina." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm=""
	ms.workload="big-data" 
	ms.date="02/12/2015"
	ms.author="raymondl,garye"/>

#Habilitar o log de serviços Web de Aprendizado de Máquina  

Este documento fornece informações sobre o recurso de log de Serviços Web AM do Azure. Habilitar o registro nos serviços Web fornece informações adicionais para ajudar a solucionar problemas das APIs, o número do erro e uma mensagem.

-	Como habilitar o log em Serviços Web:   
	-	Para habilitar o log, você precisa acessar o Painel do Serviço Web do Portal do Azure, clicar para ativá-la clicando no botão Sim e então clicar em Salvar.  
-	Qual é o efeito de habilitar o registro em log:  
	-	Quando o Registro em Log estiver habilitado, todos os diagnósticos/erros do ponto de extremidade padrão serão registrados na Conta de Armazenamento do Azure vinculada ao espaço de trabalho do usuário. Você pode ver essa conta de armazenamento no modo de exibição Painel do portal do Azure (parte inferior da seção Visão Rápida) do seu espaço de trabalho.  
-	Onde está disponível:  
	-	Atualmente, habilitamos o Registro em Log para o ponto de extremidade padrão do Serviço Web. Em breve, ofereceremos esse recurso para os pontos de extremidade adicionais que o usuário pode criar no portal do Azure.  
-	Como exibir os logs:  
	-	Os logs podem ser exibidos usando qualquer uma das várias ferramentas disponíveis para ‘explorar’ uma Conta de Armazenamento do Azure. A maneira mais fácil pode ser simplesmente navegar até a Conta de Armazenamento no portal do Azure e então clicar na guia CONTÊINERES. Em seguida, você verá um Contêiner chamado **ml-diagnostics**. Esse contêiner armazena todas as informações de diagnóstico para todos os pontos de extremidade do serviço da Web para todos os espaços de trabalho associados a esta Conta de Armazenamento.  
-	Quais são os detalhes do blob de log:  
	-	Cada blob no contêiner mantém as informações de diagnóstico para exatamente o seguinte:
		-	Uma execução do método Batch-Execution  
		-	Uma execução do método Request-Response  
		-	Inicialização de um contêiner de Request-Response  
	-	O nome de cada blob tem um prefixo no seguinte formato: {Id do Espaço de Trabalho}-{Id do serviço Web}-{Id do Ponto de Extremidade}/{Tipo de log}  
-	O tipo de log obtém um dos seguintes valores: batch score/requests score/init  


<!--HONumber=54-->