<properties 
   pageTitle="Aplicativo de API do Conector do Facebook" 
   description="Como usar o Conector do Facebook" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="adgoda"/>


# Usando o conector do Facebook em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em uma variedade de fontes de dados e oferecem conectores para obter e processar dados como uma parte do fluxo.

O Conector do Facebook permite recuperar "Nova Postagem na Linha do Tempo do Usuário", "Nova Postagem na Página", "Publicar Postagem", "Publicar Fotos" e assim por diante de sua conta do Facebook.

- O gatilho do conector do Facebook recupera “Nova Postagem na Linha do Tempo do Usuário” ou “Nova Postagem na Página”. Quando um novo tweet é recuperado, ele dispara uma nova instância do fluxo e transmite os dados recebidos na solicitação para o fluxo de processamento. 
- As ações de conector do Facebook permitem "Publicar Postagem", "Publicar Fotos" e assim por diante. Essas ações obtêm uma resposta e a disponibilizam para uso pelas ações no fluxo.

## Criando um conector do Facebook para seu aplicativo lógico ##
Para usar o conector do Facebook, você precisa primeiro criar uma instância do aplicativo de API do Conector do Facebook. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do Facebook".
3.	Configure o Conector do Facebook da seguinte maneira:
 
	![][1]
4.	Clique em OK para criar.
5.	Depois de criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do Facebook. 
	- A instância do aplicativo de API do Conector do Facebook também pode ser criada no aplicativo lógico. 
	- Abra o editor do aplicativo lógico e clique no Conector do Facebook disponível na galeria do lado direito
	- Isso criará uma instância do aplicativo de API do Conector do Facebook no mesmo grupo de recursos no qual o aplicativo lógico foi criado.


## Usando o Conector do Facebook em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o Conector do Facebook como ação/gatilho para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que contém o Conector do Facebook.
 
	![][2]
2.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. 
 
	![][3]
3.	O Conector do Facebook apareceria na seção “Usados Recentemente" na galeria à direita. Selecione-o.
 
	![][4]
4.	Você pode soltar o aplicativo de API do Conector do Facebook no editor clicando em “Conector do Facebook”, em "Usados Recentemente”, na galeria à sua direita. Clique no botão Autorizar. Forneça suas credenciais do Facebook.
  
	![][5]
5.	Permitir "Aplicativos Lógicos do Serviço de Aplicativo do Azure" 

	![][6] ![][7] ![][8]     
6.	Selecionar um gatilho.
 
	![][9]
7.	Agora, você pode usar as postagens recuperadas do gatilho do Facebook em outras ações. No fluxo abaixo, sempre que uma nova postagem é postada na linha de tempo do usuário do Facebook, a mesma postagem é publicada na linha do tempo do usuário no Twitter.
 
	![][10]
8.	De maneira semelhante, você pode criar fluxos usando as ações do Conector do Facebook. O fluxo abaixo recupera novas mensagens publicadas no grupo do Yammer e as posta na página do Facebook gerenciada pelo usuário
 
	![][11]

**DICA** - para obter a ID de página do Facebook ou a ID do grupo Yammer, procure o código numérico na url.

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!--HONumber=54--> 