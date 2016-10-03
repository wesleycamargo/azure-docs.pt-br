**O processo básico da Análise Data Lake:**
	
![Diagrama de fluxo do processo da Análise Azure Data Lake](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)
	
1. Crie uma conta da Análise Data Lake.
2. Prepare os dados de origem. Os trabalhos da Análise do Data Lake podem ler dados de contas do Repositório Azure Data Lake ou de contas de armazenamento de Blob do Azure.
3. Desenvolva um script U-SQL.
4. Envie um trabalho (script U-SQL) para a conta da Análise Data Lake. O trabalho faz a leitura dos dados de origem, processa os dados conforme as instruções no script U-SQL e salva a saída em uma conta do Repositório Azure Data Lake ou em uma conta de armazenamento de Blob.

<!---HONumber=AcomDC_0921_2016-->