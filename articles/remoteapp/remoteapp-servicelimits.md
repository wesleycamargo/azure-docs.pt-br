<properties 
    pageTitle="Limites do serviço RemoteApp"
    description="Saiba mais sobre os limites e os valores padrão para o RemoteApp do Azure" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/07/2015" 
    ms.author="elizapo" />


# Limites e valores padrão do serviço RemoteApp

Este tópico descreve os limites do serviço RemoteApp do Azure.

  
## Limites de coleção
Todas as coleções:

- Coleções por usuário: 1
- Aplicativos publicados por coleção: 5.000 

Coleções de avaliação:

- Duração da avaliação: 30 dias
- Coleções de avaliação: 2 por assinatura
- Usuários de avaliação por coleção: 10 
- Imagens de modelo de avaliação: 25
 
Coleções ativadas (pagas):

- Coleções pagas: 3 (você pode solicitar um aumento desse limite)
- Imagens de modelo pagas: 25

 
## Limites de usuário

Os limites de coleção documentada são limites flexíveis (o que significa que eles podem ser alterados). O número máximo de usuários por coleção é um limite rígido, isto é, não pode ser alterado. No entanto, o número de usuários que você pode ter é determinado pela camada de sua coleção, como se segue:


- Basic — 800 usuários
- Standard — 500 usuários

(O número de usuários com suporte é determinado pelo número de VMs usadas na sua coleção. Para a camada Basic, são 16 usuários por VM, enquanto na camada Standard, são 10 usuários por VM.)

Para calcular o número máximo de usuários atribuídos aos quais você pode oferecer suporte, multiplique o número de usuários pagos por coleção pelo número de coleções que possui.
  
Você pode criar várias coleções em sua conta com esses limites e chegar a até 5.000 conexões de usuário simultâneas em todas as coleções. Se precisar de um número maior, você poderá solicitar um aumento.

## Outros valores e limites:

- Armazenamento de dados do usuário por usuário, por coleção: 50 GB
- Tempo limite de ociosidade: 4 horas
- Tempo limite desconectado: 4 horas

## Como solicitar um aumento de limite
É possível solicitar um aumento dos limites de coleção e usuários simultâneos. Para fazer isso, abra um tíquete de suporte explicando o que é preciso.


Para abrir um tíquete de suporte, faça o seguinte:

1.	No portal de gerenciamento, clique em [Obter suporte](https://manage.windowsazure.com/?getsupport=true). Se você não estiver conectado, você será solicitado a inserir suas credenciais.
2.	Selecione sua assinatura.
3.	Em tipo de suporte, selecione **Técnico**.
4.	Clique em **Criar Tíquete**. 
5.	Selecione **RemoteApp do Azure** na lista de produtos apresentados na próxima página.
6.	Selecione um **Tipo de problema** que seja apropriado ao seu problema.
7.	Clique em **Continuar**.
8.	Siga as instruções na próxima página e insira os detalhes sobre o aumento do limite necessário. Por exemplo, se desejar estender o período de avaliação, “Por favor, estenda meu período de avaliação por X dias”. 
9.	Clique em **Enviar** para abrir o tíquete.

<!---HONumber=58--> 