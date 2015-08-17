<properties 
	pageTitle="Nome do emissor e chave do emissor nos Serviços BizTalk | Azure" 
	description="Saiba como recuperar o nome do emissor e chave do emissor para barramento de serviço ou controle de acesso (ACS) nos serviços BizTalk. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>




# Serviços BizTalk: nome e chave do emissor

Os Serviços BizTalk do Azure usam o nome e a chave do emissor do Barramento de Serviço e o nome e a chave do emissor do Controle de Acesso. Especificamente:

Tarefa | Qual nome e chave do emissor
--- | ---
Implantando seu aplicativo do Visual Studio | Nome e chave do emissor do Controle de Acesso
Configurando o Portal dos Serviços BizTalk do Azure | Nome e chave do emissor do Controle de Acesso
Criando retransmissões de LOB com os Serviços do Adaptador do BizTalk no Visual Studio | Nome e chave do emissor do Service Bus

Este tópico lista as etapas para recuperar o nome e a chave do emissor.

## Nome e chave do emissor do Controle de Acesso
O nome e a chave do emissor do Controle de Acesso são usados pelo seguinte:

- Seu aplicativo do Serviço do BizTalk do Azure criado no Visual Studio: para implantar seu aplicativo do Serviço do BizTalk no Visual Studio para Azure, você insere o nome e a chave do emissor do Controle de Acesso. 
- O Portal de Serviços BizTalk do Azure: quando você cria um Serviço BizTalk e abre o Portal de Serviços BizTalk, o Nome de emissor do Controle de Acesso e a Chave do emissor são automaticamente registrados para implantações com os mesmos valores de Controle de Acesso.

### Para copiar e colar o Nome e a Chave do emissor do Controle de Acesso

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação à esquerda, selecione **Serviços do BizTalk**.
3. Selecione o Serviço do BizTalk. 
4. Selecione **Informação de Conexão** na barra de tarefas. O Namespace do Controle de Acesso, o Emissor Padrão (Nome do Emissor) e a Chave Padrão (Chave do Emissor) são listados e podem ser copiados e colados.<br/><br/> Resumindo:<br/> Nome do emissor = Emissor padrão<br/> Chave do emissor = Chave padrão


Você também pode selecionar **Abrir Portal de Gerenciamento ACS** para recuperar os valores de Controle de Acesso:

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação à esquerda, selecione **Serviços do BizTalk**.
3. Selecione o Serviço do BizTalk.
4. Selecione o botão Informações de Conexão e selecione **Abrir Portal de Gerenciamento ACS**.
5. No Portal, em **Configurações de Serviço**, selecione ** Identidades de Serviço**. Isso exibe sua Identidade de Serviço, que é o valor do Nome do Emissor do Controle de Acesso. Selecione o link da Identidade do Serviço para consultar a Senha, que é o valor da Chave do Emissor. Os valores podem ser copiados.<br/><br/> Por exemplo, em **Identidades de Serviço**, você verá "proprietário". "Proprietário" é o nome do emissor de Controle de Acesso. Quando você clica no link "proprietário", você verá a **Senha**. Quando você clica no link "Senha", você verá o valor. O valor dessa Senha é sua Chave do Emissor do Controle de Acesso. <br/><br/> Resumindo:<br/> Nome do Emissor = nome da Identidade do Serviço<br/> Chave do Emissor = valor da Senha

No painel de navegação à esquerda, você também pode selecionar **Active Directory** para recuperar os valores do Controle de Acesso.

> [AZURE.IMPORTANT]Quando o namespace do Controle de Acesso é criado usando o **Active Directory**, uma Identidade de Serviço **não** é criada automaticamente. Quando você provisiona um Serviço BizTalk, um Namespace de Controle de Acesso, uma Identidade de Serviço chamada "proprietário" (Nome do Emissor), uma Senha (Chave do Emissor) e uma Chave Simétrica são criados automaticamente.<br /> [Como usar o Serviço de Gerenciamento do ACS para configurar identidades de serviço](http://go.microsoft.com/fwlink/p/?LinkID=303942) fornece mais informações sobre identidades de serviço do Controle de Acesso.


## Nome e chave do emissor do Barramento de Serviço
O nome e a chave do emissor do Barramento de Serviço são usados pelos Serviços do Adaptador do BizTalk. Em seu projeto de Serviços BizTalk no Visual Studio, você usa os Serviços do Adaptador BizTalk para se conectar a um sistema de Linha de Negócios (LOB) local. Para se conectar, você cria a retransmissão de LOB e insere os detalhes do seu sistema de LOB. Ao fazer isso, você também insere o nome e a chave do emissor do Barramento de Serviço.

### Para recuperar o nome e a chave do emissor do Barramento de Serviço

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Barramento de Serviço**.
3. Selecione seu namespace. Na barra de tarefas, selecione **Informações da Conexão**. Isso exibe o **Emissor padrão** (Nome do Emissor) e a **Chave Padrão** (Chave do Emissor). Os valores podem ser copiados.<br/><br/> Resumindo:<br/> Nome do Emissor = Emissor Padrão<br/> Chave do Emissor = Chave Padrão

## Avançar
Tópicos adicionais sobre Serviços BizTalk do Azure:

-  [Instalando o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriais: Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## Consulte também
-  [Como usar o Serviço de Gerenciamento do ACS para configurar identidades de serviço](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços BizTalk: gráfico do status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços BizTalk: guias Painel, Monitor e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Serviços BizTalk: backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=August15_HO6-->