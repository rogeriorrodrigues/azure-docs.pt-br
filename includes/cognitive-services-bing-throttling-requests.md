O serviço e o seu tipo de subscrição determinam o número de consultas por segundo (QPS) que você pode efetuar. Verifique se seu aplicativo inclui a lógica para permanecer dentro da sua cota. Se o limite de QPS for atingido ou excedido, a solicitação falhará e um código de status HTTP 429 será retornado. A resposta inclui o cabeçalho `Retry-After`, que indica o quanto você deve aguardar antes de enviar outra solicitação.

## <a name="denial-of-service-versus-throttling"></a>Negação de serviço versus limitação

O serviço faz uma diferenciação entre um ataque de negação de serviço (DoS) e uma violação QPS. Se o serviço suspeitar de um ataque DoS, a solicitação tem êxito (o código de status HTTP é 200 OK); no entanto, o corpo da resposta está vazio.