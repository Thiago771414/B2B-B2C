# B2B (Business-to-Business) e B2C (Business-to-Consumer)

O Adobe Acrobat Sign é uma solução de assinatura eletrônica fornecida pela Adobe Systems. Ela permite que empresas e consumidores assinem documentos de forma digital, eliminando a necessidade de impressão, assinatura manual e envio físico. O Adobe Acrobat Sign é adequado tanto para cenários de negócios entre empresas (B2B) quanto para transações entre empresas e consumidores (B2C).

No contexto B2B, o Adobe Acrobat Sign permite que empresas realizem processos de assinatura digital de forma eficiente e segura. As empresas podem enviar contratos, acordos e outros documentos importantes para clientes, parceiros comerciais ou fornecedores. O Adobe Acrobat Sign simplifica o fluxo de trabalho ao automatizar o processo de assinatura, reduzindo o tempo necessário para obter assinaturas e agilizando os negócios.

No cenário B2C, o Adobe Acrobat Sign oferece aos consumidores a capacidade de assinar documentos eletronicamente de maneira rápida e conveniente. Isso pode incluir contratos de locação, acordos de serviços, formulários de inscrição e muito mais. Os consumidores podem assinar documentos facilmente usando dispositivos móveis, como smartphones ou tablets, proporcionando uma experiência sem complicações.

Agora, para ilustrar o uso do Adobe Acrobat Sign, vamos fornecer um exemplo de código em csharp para enviar um documento para assinatura usando a API REST do Adobe Sign:

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;

class Program
{
    static HttpClient client = new HttpClient();

    static async System.Threading.Tasks.Task Main(string[] args)
    {
        string clientId = "SEU_CLIENT_ID";
        string clientSecret = "SEU_CLIENT_SECRET";
        string filePath = "caminho_para_seu_arquivo.pdf";
        string recipientEmail = "exemplo@email.com";

        string accessToken = await GetAccessToken(clientId, clientSecret);
        string transientDocId = await UploadDocument(filePath, accessToken);
        string agreementId = await SendForSignature(transientDocId, recipientEmail, accessToken);

        Console.WriteLine("Documento enviado para assinatura. ID do acordo: " + agreementId);
    }

    static async System.Threading.Tasks.Task<string> GetAccessToken(string clientId, string clientSecret)
    {
        var request = new HttpRequestMessage(HttpMethod.Post, "https://api.echosign.com/oauth/token");
        request.Content = new StringContent($"client_id={clientId}&client_secret={clientSecret}&grant_type=client_credentials");
        request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");

        var response = await client.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        dynamic json = Newtonsoft.Json.JsonConvert.DeserializeObject(content);
        return json.access_token;
    }

    static async System.Threading.Tasks.Task<string> UploadDocument(string filePath, string accessToken)
    {
        var request = new HttpRequestMessage(HttpMethod.Post, "https://api.echosign.com/api/rest/v6/transientDocuments");
        request.Headers.Add("Access-Token", accessToken);

        var fileContent = new StreamContent(File.OpenRead(filePath));
        var fileFormDataContent = new MultipartFormDataContent();
        fileFormDataContent.Add(fileContent, "File", Path.GetFileName(filePath));

        request.Content = fileFormDataContent;

        var response = await client.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        dynamic json = Newtonsoft.Json.JsonConvert.DeserializeObject(content);
        return json.transientDocumentId;
    }

    static async System.Threading.Tasks.Task<string> SendForSignature(string transientDocId, string recipientEmail, string accessToken)
    {
        var request = new HttpRequestMessage(HttpMethod.Post, "https://api.echosign.com/api/rest/v6/agreements");
        request.Headers.Add("Access-Token", accessToken);

        var payload = new
        {
            fileInfos = new[]
            {
                new
                {
                    transientDocumentId = transientDocId
                }
            },
            recipients = new[]
            {
                new
                {
                    email = recipientEmail
                }
            }
        };

        string payloadJson = Newtonsoft.Json.JsonConvert.SerializeObject(payload);
        request.Content = new StringContent(payloadJson);
        request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        var response = await client.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        dynamic json = Newtonsoft.Json.JsonConvert.DeserializeObject(content);
        return json.id;
    }
}
```
## ID de Rastreabilidade
A rastreabilidade se refere à capacidade de rastrear e acompanhar informações em um sistema, identificando a origem e o destino de uma determinada ação ou evento. Um ID de rastreabilidade é um identificador único associado a uma ação ou evento específico, permitindo que seja rastreado ao longo do sistema.
Por exemplo, em um sistema distribuído com vários microsserviços, cada solicitação ou transação pode ser associada a um ID de rastreabilidade exclusivo. Esse ID pode ser usado para registrar informações relacionadas à solicitação em cada serviço que ela passa, permitindo que você acompanhe todo o caminho da solicitação e identifique possíveis problemas ou gargalos.

O ID de rastreabilidade é especialmente útil em sistemas complexos, onde várias partes estão envolvidas e é necessário ter uma visibilidade clara do fluxo de informações e eventos.

```csharp
https://api.echosign.com/api/rest/v6/agreements?apiKey=SUA_CHAVE_API
```
## Idempotência
A idempotência é um conceito importante para garantir que uma operação seja executada apenas uma vez, mesmo que seja solicitada várias vezes. No caso do Adobe Acrobat Sign, podemos simular a idempotência atribuindo um ID único a cada solicitação de assinatura. Em seguida, podemos verificar se o ID já existe antes de enviar a solicitação. Se o ID já existir, podemos retornar o resultado anterior em vez de enviar uma nova solicitação.

```csharp
string agreementId = GetAgreementIdFromDatabase(); // Obter o ID da solicitação anterior da base de dados

if (string.IsNullOrEmpty(agreementId))
{
    agreementId = SendForSignature(file_path, recipient_email); // Enviar uma nova solicitação
    SaveAgreementIdToDatabase(agreementId); // Salvar o ID da solicitação na base de dados
}

return agreementId;

```
## Async API:
O uso de uma API assíncrona é benéfico quando se espera que as operações tenham um tempo de resposta prolongado. Podemos simular uma chamada assíncrona para a API do Adobe Acrobat Sign usando o modificador async e await no método de envio de solicitação.

```csharp
async Task<string> SendForSignatureAsync(string file_path, string recipient_email)
{
    // Código de configuração e autenticação do Adobe Sign...

    var response = await httpClient.PostAsync(api_url, content);
    var agreementId = await response.Content.ReadAsStringAsync();

    return agreementId;
}
```

## API First:
O conceito de API First envolve projetar e desenvolver a API antes de qualquer outra camada do sistema. Podemos simular esse conceito criando uma especificação da API do Adobe Acrobat Sign usando o formato OpenAPI (anteriormente conhecido como Swagger). Em seguida, podemos gerar automaticamente os controladores e os modelos C# usando ferramentas como o Swagger Codegen.

```csharp
// Arquivo da especificação OpenAPI do Adobe Acrobat Sign (adobe-acrobat-sign-api.yaml)

// Executar o Swagger Codegen para gerar os controladores e modelos C#
swagger-codegen generate -i adobe-acrobat-sign-api.yaml -l csharp
```
## Event-Driven Architecture:
A arquitetura orientada a eventos permite que os componentes se comuniquem de forma assíncrona por meio de eventos. Podemos simular a arquitetura orientada a eventos no contexto do Adobe Acrobat Sign usando um sistema de mensagens para publicar e consumir eventos relacionados às assinaturas.

```csharp
// Publicar um evento de assinatura
string agreementId = SendForSignature(file_path, recipient_email);
PublishEvent("AssinaturaEnviada", agreementId);

// Consumir eventos de assinatura
SubscribeToEvent("AssinaturaEnviada", (agreementId) =>
{
    // Lógica para processar a assinatura enviada
    ProcessAgreement(agreementId);
});
```
## Telemetria
A telemetria é importante para monitorar o desempenho, a disponibilidade e o uso da API do Adobe Acrobat Sign. Podemos simular a telemetria registrando métricas e eventos relevantes ao longo da execução do código.

```csharp
// Registrar uma métrica de tempo de resposta
var stopwatch = new Stopwatch();
stopwatch.Start();
string agreementId = SendForSignature(file_path, recipient_email);
stopwatch.Stop();
Telemetry.TrackMetric("TempoRespostaAssinatura", stopwatch.ElapsedMilliseconds);

// Registrar um evento de uso
Telemetry.TrackEvent("AssinaturaEnviada", new Dictionary<string, string>
{
    { "AgreementId", agreementId },
    { "RecipientEmail", recipient_email }
});
```
## Circuit Breaker
O Circuit Breaker é um padrão de projeto que permite evitar o impacto de falhas temporárias em serviços remotos. Podemos simular o uso do Circuit Breaker ao lidar com falhas na API do Adobe Acrobat Sign, fornecendo uma resposta alternativa ou retornando um valor em cache quando a API estiver inacessível.

```csharp
// Tentar enviar uma solicitação de assinatura
try
{
    string agreementId = SendForSignature(file_path, recipient_email);
    return agreementId;
}
catch (ApiUnavailableException)
{
    string agreementId = GetAgreementIdFromCache();
    return agreementId;
}
```

## Adobe Acrobat Sign
![Imagem](https://www.adobe.com/content/dam/dx-dc/us/en/acrobat/1_Device_Marquee.png.img.png)
