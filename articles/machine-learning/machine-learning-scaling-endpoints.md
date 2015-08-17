<properties 
	pageTitle="Escala de pontos de extremidade de API | Microsoft Azure" 
	description="A escala de pontos de extremidade de serviço Web no Aprendizado de Máquina do Azure" 
	services="machine-learning"
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="06/29/2015"
	ms.author="himad"/>


# Pontos de extremidade da API de Escala

Os pontos de extremidade do serviço Web no Aprendizado de Máquina do Azure têm níveis de limitação selecionável para coincidir com a taxa na qual o ponto de extremidade será consumido.

Há dois fatores que controlam a quantidade de limitação feita em um ponto de extremidade - Nível de limitação: Baixa ou Alta. Somente clientes que pagam são permitidos para definir o nível de limitação para Alto - Máximo de Chamadas Simultâneas: 4 para o nível de limitação Baixo; 20 a 200 para o nível de limitação Alto


As APIs síncronas são normalmente usadas em situações onde uma baixa latência é desejada. A latência aqui indica o tempo necessário para a API concluir uma solicitação e não se responsabiliza por quaisquer atrasos na rede. Digamos que você tenha uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com alto nível de limitação e o Máximo de Chamadas Simultâneas = 20, você precisa chamar esta API 20 * 1000 / 50 = 400 vezes por segundo. Estendendo isso ainda mais, um Máximo de Chamadas Simultâneas de 200 permitirá que você chame a API 4000 vezes por segundo, supondo que a latência é de 50 ms.

Se você planeja chamar a API com uma carga maior que o Máximo de Chamadas Simultâneas de 200 terá suporte, deverá criar vários pontos de extremidade no mesmo serviço Web e aleatoriamente distribuir a carga entre todos eles.

Tenha em mente que pode ser prejudicial se você não estiver acessando a API com uma taxa alta de forma correspondente usando uma contagem de simultaneidade muito alta. Você pode ver tempos limite esporádicos e/ou picos na latência se colocar uma carga relativamente baixa em uma API configurada para alta carga.

Observe que apenas ajustar configurações de limitação influencia o comportamento da API Síncrona (RRS). Você deve ajustar essas configurações se vir respostas frequentes de Serviço Indisponível 503 na API Síncrona.

A interface de usuário de gerenciamento permite alternar o nível de limitação. Para ter um número de simultaneidade personalizado para limitar o Nível Alto, use a API de Ponto de Extremidade de Patch.

- Abra manage.windowsazure.com
- Navegue até a guia Aprendizado de Máquina
- Clique em seu espaço de trabalho.
- Navegue para o serviço Web que tem o ponto de extremidade ![Navegue até o serviço Web](./media/machine-learning-scaling-endpoints/figure-1.png)

- Clique no ponto de extremidade e, em seguida, clique na guia Configurar ![Navegue para a configuração do ponto de extremidade](./media/machine-learning-scaling-endpoints/figure-2.png)


- Altere o nível de limitação para Alto e clique em Salvar.


 

<!---HONumber=August15_HO6-->