## <a name="test-the-function"></a>Testar a função

Use o cURL para testar a função implantada em um computador Mac ou Linux, ou usando Bash no Windows. Execute o seguinte comando cURL, substituindo o espaço reservado `<app_name>` pelo nome do aplicativo de funções. Acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Resposta de função mostrada em um navegador.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Caso não tenha o cURL disponível na linha de comando, digite a mesma URL no endereço do seu navegador da Web. Novamente, substitua o espaço reservado `<app_name>` pelo nome do aplicativo de funções, e acrescente a cadeia de consulta `&name=<yourname>` à URL e execute a solicitação. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Resposta de função mostrada em um navegador.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
