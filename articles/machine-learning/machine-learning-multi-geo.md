<properties
   pageTitle="Documentação de ajuda de várias áreas geográficas | Microsoft Azure"
   description="Saiba como criar um espaço de trabalho e publicar um serviço Web em uma região do Azure diferente do Centro-Sul dos Estados Unidos (SCUS)."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="paulettm"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/23/2015"
   ms.author="tedway; neerajkh"/>

# Documentação da Ajuda para diversas áreas geográficas

Este artigo descreve como criar um espaço de trabalho e publicar um serviço Web em outras regiões do Azure.

## Criar um espaço de trabalho

1. Entre no Portal de Gerenciamento do Azure.

2.  Clique em **+NOVO** > **SERVIÇOS DE DADOS** > **APRENDIZADO DE MÁQUINA** > **CRIAÇÃO RÁPIDA**. Em **LOCAL**, selecione outra região, como **Sudeste da Ásia**. ![Imagem de Ajuda de várias áreas geográficas 1][1]
3. Selecione o espaço de trabalho e, em seguida, clique em **Entrar no Estúdio AM**. ![Imagem de Ajuda de várias áreas geográficas 2][2]

4. Agora você tem um espaço de trabalho em outra região que pode usar como qualquer outro espaço de trabalho. Para alternar entre seus espaços de trabalho, veja o canto superior direito da tela. Clique na lista suspensa, selecione a região e, em seguida, selecione o espaço de trabalho. Tudo é local à região do espaço de trabalho; por exemplo, todos os serviços Web criados por meio de um espaço de trabalho estarão na mesma região em que o espaço de trabalho está localizado. ![Imagem de Ajuda de várias áreas geográficas 3][3]

## Abrir um experimento da Galeria

Se você abrir um experimento da galeria, também pode selecionar a região para a qual deseja copiar o experimento.

![Imagem de Ajuda de várias áreas geográficas 4][4a]

## Gerenciamento de serviço Web

Para gerenciar programaticamente os serviços Web, como novo treinamento, use o endereço específico de região: * ***https://asiasoutheast.management.azureml.net**

### Elementos a serem observados

1.	Você só pode copiar experimentos entre espaços de trabalho que pertençam à mesma região. No futuro, habilitaremos a cópia de experimentos entre espaços de trabalho através de várias regiões.
2.	O seletor de região mostrará apenas os espaços de trabalho para uma região por vez. No futuro, você poderá ver uma lista completa de espaços de trabalho aos quais tem acesso em todas as regiões ao mesmo tempo.  
3.	Um espaço de trabalho livre ou de acesso de convidado (anônimo) será criado e hospedado no Centro-Sul dos EUA No futuro, você poderá criar espaços de trabalho de acesso livre/de convidado na região que escolher.  
4.	Os serviços Web implantados por meio de um espaço de trabalho no Sudeste da Ásia também serão hospedados no Sudeste da Ásia. No futuro, você terá a flexibilidade de criar experiências em uma região e implantar pontos de extremidade de serviço Web gerados em regiões diferentes.  

## Mais informações

Fazer uma pergunta no [fórum de Aprendizado de Máquina do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<!---HONumber=AcomDC_1125_2015-->