<properties
   pageTitle="Pacotes NuGet | Microsoft Azure"
   description="Orientação sobre pacotes NuGet para o trabalho de política de repetição geral."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# Pacotes NuGet

<p class="lead">À medida que mais componentes começam a se comunicar, torna-se mais importante saber tratar as falhas transitórias com inteligência. O trabalho Tratamento de Falha Transitória tratado pelo pacote NuGet das políticas de repetição pode ajudar a tratar repetições em uma única instância.</p>

> Este documento foi baseado em um rascunho como uma prova de conceito. Não se trata da diretriz revisada real.

O código `TransientFaultHandling` de Padrões e Práticas é recomendado para o trabalho de política de repetição geral.

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## Configuração

A seção inclui as informações de configuração para o recurso de repetição:

Parâmetro | Descrição
-------------------- | ----------------------
MaximumExecutionTime | Tempo máximo de execução da solicitação, incluindo todas as possíveis tentativas de repetição.
ServerTimeOut | Intervalo de tempo limite do servidor para a solicitação
RetryPolicy | Política de repetição Consulte a seção Políticas, abaixo

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

Programático:

- Suporte para configuração no Cliente.
- Habilitar substituição em operações fornecidas pelo cliente

Arquivo de configuração:

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## Políticas

### Exponencial

Usada para espaçamento entre as tentativas repetidas das invocações de serviços exponencialmente para evitar limitação do serviço.

__Abordagem:__

Aumente exponencialmente o intervalo o intervalo de retirada entre tentativas subsequentes. Adicione aleatoriedade ao intervalo de retirada (+/- 20%) para evitar que todos os clientes repitam simultaneamente

__Configuração:__

Parâmetro | Descrição
-------------------- | -------------------------------------------------------
maxAttempt | Número de tentativas de repetição.
deltaBackoff | Intervalo de retirada entre repetições. Múltiplos desse período de tempo serão usados para tentativas de repetição subsequentes.
MinBackoff | Adicionado a todos os intervalos de repetição calculados de deltaBackoff.
FastFirst | Primeira tentativa imediata
MaxBackoff | MaxBackoff será usado se o intervalo de repetição calculado for maior que MaxBackoff. Este valor não pode ser alterado.

__Lógica da implementação:__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Linear

Usada para espaçamento entre as tentativas repetidas das invocações de serviços linearmente para evitar limitação do serviço.

__Abordagem:__

Execute um número especificado de repetições usando um intervalo de tempo fixo especificado entre as repetições. Adicione aleatoriedade ao intervalo de retirada (+/- 20%) para evitar que todos os clientes repitam simultaneamente.

__Configuração:__

Parâmetro | Descrição
-------------------- | -------------------------------------------------------
maxAttempt | Número de tentativas de repetição.
deltaBackoff | Intervalo de retirada entre repetições.
FastFirst | Primeira tentativa imediata

__Lógica da implementação:__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Adaptável

Usada para espaçamento de tentativas repetidas de invocações de serviço com base no código de erro/metadados passado pelo serviço no cabeçalho da resposta.

__Abordagem:__

Executar um determinado número de repetições usando um intervalo de retirada calculado com base no código de erro/metadados passado pelo serviço no cabeçalho da resposta


__Configuração:__

Não configurável

__Lógica da implementação:__

Com base no código de erro/metadados passado pelo serviço no cabeçalho da resposta

__Quebra de circuito:__

Com base em [Disjuntor](http://msdn.microsoft.com/library/dn589784.aspx)

## Extensibilidade

Interface pública que pode ser implementada para fornecer a política de repetição personalizada

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## Telemetria

Registre entradas como eventos ETW usando um EventSource. Veja a seguir os campos que devem ser registrados em log para cada tentativa de repetição

Parâmetro | Descrição
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
operation | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "5/9/2014 22:00:13"
operationEndTime | "5/9/2014 22:00:14"
iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "Não foi possível resolver o nome remoto: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=July15_HO4-->