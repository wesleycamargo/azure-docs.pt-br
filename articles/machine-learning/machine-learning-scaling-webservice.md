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
   ms.date="07/06/2016"
   ms.author="neerajkh"/>

# Dimensionando um serviço Web

>[AZURE.NOTE] Este tópico descreve técnicas aplicáveis a um serviço Web clássico.

Por padrão, cada serviço Web publicado é configurado para oferecer suporte a 20 a 200 solicitações simultâneas. Embora o Portal Clássico do Azure forneça uma maneira de definir esse valor, o Aprendizado de Máquina do Azure otimiza automaticamente essa configuração para fornecer o melhor desempenho para o serviço Web e o valor do portal é ignorado.

Se você planeja chamar a API com uma carga maior que o Máximo de Chamadas Simultâneas de 200 terá suporte, deverá criar vários pontos de extremidade no mesmo serviço Web e aleatoriamente distribuir a carga entre todos eles.

## Adicionar novos pontos de extremidade ao mesmo serviço Web

O dimensionamento de um serviço Web é uma tarefa comum para oferecer suporte a mais de 200 solicitações simultâneas, aumentar a disponibilidade por meio de vários pontos de extremidade ou fornecer um ponto de extremidade separado para outro consumidor de serviço Web. Você pode aumentar a escala adicionando pontos de extremidade para o mesmo serviço Web por meio do [Portal Clássico do Azure](https://manage.windowsazure.com/) conforme mostrado na figura a seguir:

Tenha em mente que pode ser prejudicial se você não estiver acessando a API com uma taxa alta de forma correspondente usando uma contagem de simultaneidade muito alta. Você pode ver tempos limite esporádicos e/ou picos na latência se colocar uma carga relativamente baixa em uma API configurada para alta carga.

As APIs síncronas são normalmente usadas em situações onde uma baixa latência é desejada. A latência aqui indica o tempo necessário para a API concluir uma solicitação e não se responsabiliza por quaisquer atrasos na rede. Digamos que você tenha uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com alto nível de limitação e o Máximo de Chamadas Simultâneas = 20, você precisa chamar esta API 20 * 1000 / 50 = 400 vezes por segundo. Estendendo isso ainda mais, um Máximo de Chamadas Simultâneas de 200 permitirá que você chame a API 4000 vezes por segundo, supondo que a latência é de 50 ms.

Vá para o [Portal Clássico do Azure](https://manage.windowsazure.com/), clique no ícone do Aprendizado de Máquina à esquerda, selecione o espaço de trabalho usado para publicar o serviço Web, clique no serviço Web desejado, clique em **ADICIONAR PONTO DE EXTREMIDADE** no painel inferior e forneça um nome, descrição e simultaneidade desejada para o novo ponto de extremidade.

Para adicionar novos pontos de extremidade, consulte [Criando pontos de extremidade](machine-learning-create-endpoint.md).

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0720_2016-->