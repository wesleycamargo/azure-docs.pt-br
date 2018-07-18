O serviço e o seu tipo de subscrição determinar o número de consultas que você pode efetuar por segundo (QPS). Certifique-se de que seu aplicativo inclui a lógica necessária para permanecer dentro da sua cota. Se você exceder seu QPS, a solicitação falhará com o código de status HTTP 429. A resposta também inclui o cabeçalho Retry-After, que contém o número de segundos que você deve esperar antes de enviar outra solicitação.  
  
### <a name="denial-of-service-dos-versus-throttling"></a>Negação de Serviço (DOS) versus Limitação

O serviço faz distinção entre um ataque DOS e violação QPS. Se o serviço suspeitar de um ataque de negação de serviço, a solicitação é bem-sucedida (o código de status HTTP é 200 OK); no entanto, o corpo da resposta está vazio.