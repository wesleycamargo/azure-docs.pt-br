<properties
   pageTitle="Dimensionando serviço Web | Microsoft Azure"
   description="Saiba como dimensionar um serviço Web, aumentando a simultaneidade e adicionando novos pontos de extremidade."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="aprendizado de máquina do azure, serviços Web, operacionalização, dimensionamento, ponto de extremidade, simultaneidade"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/27/2015"
   ms.author="neerajkh"/>

# Dimensionando serviço Web

## Aumentar a simultaneidade

Por padrão, cada serviço Web publicado é configurado para oferecer suporte a 20 solicitações simultâneas. Você pode aumentar essa simultaneidade para 200 solicitações simultâneas por meio do [Portal Clássico do Azure](https://manage.windowsazure.com/), como mostrado na figura abaixo.

Vá para o [Portal Clássico do Azure](https://manage.windowsazure.com/), clique no ícone do Aprendizado de Máquina à esquerda, selecione o espaço de trabalho usado para publicar o serviço Web, clique no serviço desejado, selecione o ponto de extremidade que precisa ser aumentado em simultaneidade e, em seguida, clique em **CONFIGURAR**. Use o controle deslizante para aumentar a simultaneidade e, em seguida, clique em **SALVAR** no painel inferior.

Para aumentar a simultaneidade, consulte [Pontos de extremidade de API de dimensionamento](machine-learning-scaling-endpoints.md).

   ![Aprendizado de máquina, pontos de extremidade de dimensionamento.][1]

## Adicionar novos pontos de extremidade ao mesmo serviço Web

O dimensionamento de serviço Web é uma tarefa comum para oferecer suporte a mais de 200 solicitações simultâneas, aumentar a disponibilidade por meio de vários pontos de extremidade ou fornecer um ponto de extremidade separado para outro consumidor de serviço Web. O usuário pode aumentar a escala com a adição de pontos de extremidade ao mesmo serviço Web. O usuário pode adicionar outros pontos de extremidade por meio do [Portal Clássico do Azure](https://manage.windowsazure.com/), como mostrado na seguinte figura:

Vá para o [Portal Clássico do Azure](https://manage.windowsazure.com/), clique no ícone do Aprendizado de Máquina à esquerda, selecione o espaço de trabalho usado para publicar o serviço Web, clique no serviço Web desejado, clique em **ADICIONAR PONTO DE EXTREMIDADE** no painel inferior e forneça um nome, descrição e simultaneidade desejada para o novo ponto de extremidade.

Para adicionar novos pontos de extremidade, consulte [Criando pontos de extremidade](machine-learning-create-endpoint.md).

   ![Aprendizado de máquina, adicionar novos pontos de extremidade.][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_1203_2015-->