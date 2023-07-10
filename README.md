# B2B (Business-to-Business) e B2C (Business-to-Consumer)

O Adobe Acrobat Sign é uma solução de assinatura eletrônica fornecida pela Adobe Systems. Ela permite que empresas e consumidores assinem documentos de forma digital, eliminando a necessidade de impressão, assinatura manual e envio físico. O Adobe Acrobat Sign é adequado tanto para cenários de negócios entre empresas (B2B) quanto para transações entre empresas e consumidores (B2C).

No contexto B2B, o Adobe Acrobat Sign permite que empresas realizem processos de assinatura digital de forma eficiente e segura. As empresas podem enviar contratos, acordos e outros documentos importantes para clientes, parceiros comerciais ou fornecedores. O Adobe Acrobat Sign simplifica o fluxo de trabalho ao automatizar o processo de assinatura, reduzindo o tempo necessário para obter assinaturas e agilizando os negócios.

No cenário B2C, o Adobe Acrobat Sign oferece aos consumidores a capacidade de assinar documentos eletronicamente de maneira rápida e conveniente. Isso pode incluir contratos de locação, acordos de serviços, formulários de inscrição e muito mais. Os consumidores podem assinar documentos facilmente usando dispositivos móveis, como smartphones ou tablets, proporcionando uma experiência sem complicações.

Agora, para ilustrar o uso do Adobe Acrobat Sign, vamos fornecer um exemplo de código em Python para enviar um documento para assinatura usando a API REST do Adobe Sign:

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

## Adobe Acrobat Sign
![Imagem](https://www.adobe.com/content/dam/dx-dc/us/en/acrobat/1_Device_Marquee.png.img.png)
