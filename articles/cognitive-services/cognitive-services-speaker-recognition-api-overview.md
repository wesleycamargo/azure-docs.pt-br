<properties
   pageTitle="Visão geral: API de Reconhecimento do Locutor dos Serviços Cognitivos | Microsoft Azure"
   description="Saiba como o reconhecimento do locutor pode aprimorar os aplicativos. As APIs de Reconhecimento do Locutor trazem algoritmos avançados para a verificação e identificação do locutor."
   services="cognitive-services"
   documentationCenter="na"
   authors="cjgronlund"
   manager="paulettem"
   editor="cjgronlund"/>

<tags
   ms.service="cognitive-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="cgronlun"/>

# Visão geral: API de Reconhecimento do Locutor dos Serviços Cognitivos do Azure

Saiba como as funcionalidades de reconhecimento do locutor podem aprimorar os aplicativos. APIs de Reconhecimento do Locutor são APIs baseadas em nuvem que fornecem os algoritmos mais avançados para a verificação e identificação do locutor. O Reconhecimento do Locutor pode ser dividido em duas categorias:

  * Verificação do locutor
  * Identificação do locutor

## Verificação do locutor

A voz tem características exclusivas que podem ser usadas para identificar uma pessoa, assim como uma impressão digital. O uso da voz como um sinal para cenários de autenticação e controle de acesso surgiu como uma nova ferramenta inovadora – oferecendo, basicamente, mais um nível na segurança que simplifica a experiência de autenticação para os clientes.

As APIs de Verificação do Locutor podem verificar e autenticar usuários usando suas vozes ou falas automaticamente.

### Registro de verificação do locutor

O registro de verificação do locutor depende do texto, o que significa que os locutores precisam escolher uma frase secreta específica que será usada durante as fases de registro e de verificação.

No registro, a voz do locutor é registrada com uma frase específica e, em seguida, vários recursos são extraídos e a frase escolhida é reconhecida. Juntos, os recursos extraídos e a frase escolhida formam uma assinatura de voz exclusiva.

### Verificando a voz e a frase de entrada

Na verificação, uma voz e frase de entrada são comparadas com a assinatura de voz e a frase do registro, a fim de verificar se elas são da mesma pessoa e se estão falando a frase correta.

Para obter mais detalhes sobre a verificação do locutor, consulte a [parte sobre Verificação da documentação da API de Reconhecimento do Locutor](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## Identificação do locutor

As APIs de Identificação do Locutor podem identificar automaticamente a pessoa que está falando em um arquivo de áudio, dado um grupo de locutores potenciais. O áudio de entrada é emparelhado com um grupo de locutores fornecidos e, caso seja encontrada uma correspondência, a identidade do locutor será retornada.

Todos os locutores devem passar por um processo de registro para que suas vozes sejam registradas no sistema e ter uma impressão de voz criada.

### Registro de identificação do locutor

O registro de identificação do locutor não depende do texto, o que significa que não há restrições sobre o que é dito pelo locutor no áudio. A voz do orador é registrada e vários recursos são extraídos para formar uma assinatura de voz exclusiva.

### Reconhecimento do locutor: fazendo a correspondência com a voz de entrada

O áudio de orador desconhecido, junto com o grupo potencial de locutores, é fornecido durante o reconhecimento. A voz de entrada é comparada com todos os locutores para determinar o autor dessa voz e, se for encontrada uma correspondência, a identidade do orador será retornada.

Para obter mais detalhes sobre a identificação do locutor, consulte a [parte sobre Identificação da documentação da API de Reconhecimento do Locutor](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).

<!---HONumber=AcomDC_0504_2016-->