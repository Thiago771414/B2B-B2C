# B2B (Business-to-Business) e B2C (Business-to-Consumer)

O Adobe Acrobat Sign é uma solução de assinatura eletrônica fornecida pela Adobe Systems. Ela permite que empresas e consumidores assinem documentos de forma digital, eliminando a necessidade de impressão, assinatura manual e envio físico. O Adobe Acrobat Sign é adequado tanto para cenários de negócios entre empresas (B2B) quanto para transações entre empresas e consumidores (B2C).

No contexto B2B, o Adobe Acrobat Sign permite que empresas realizem processos de assinatura digital de forma eficiente e segura. As empresas podem enviar contratos, acordos e outros documentos importantes para clientes, parceiros comerciais ou fornecedores. O Adobe Acrobat Sign simplifica o fluxo de trabalho ao automatizar o processo de assinatura, reduzindo o tempo necessário para obter assinaturas e agilizando os negócios.

No cenário B2C, o Adobe Acrobat Sign oferece aos consumidores a capacidade de assinar documentos eletronicamente de maneira rápida e conveniente. Isso pode incluir contratos de locação, acordos de serviços, formulários de inscrição e muito mais. Os consumidores podem assinar documentos facilmente usando dispositivos móveis, como smartphones ou tablets, proporcionando uma experiência sem complicações.

Agora, para ilustrar o uso do Adobe Acrobat Sign, vamos fornecer um exemplo de código em Python para enviar um documento para assinatura usando a API REST do Adobe Sign:

```csharp
import requests
import json

# Definir as credenciais da API do Adobe Sign
client_id = "SEU_CLIENT_ID"
client_secret = "SEU_CLIENT_SECRET"

# Obter um access token usando as credenciais
def get_access_token():
    auth_url = "https://api.echosign.com/oauth/token"
    payload = {
        "client_id": client_id,
        "client_secret": client_secret,
        "grant_type": "client_credentials"
    }
    response = requests.post(auth_url, data=payload)
    access_token = json.loads(response.content)["access_token"]
    return access_token

# Enviar documento para assinatura
def send_for_signature(file_path, recipient_email):
    access_token = get_access_token()
    api_url = "https://api.echosign.com/api/rest/v6/transientDocuments"
    
    # Fazer upload do documento para a API
    headers = {
        "Access-Token": access_token,
        "Content-Type": "application/pdf"
    }
    files = {
        "File": open(file_path, "rb")
    }
    response = requests.post(api_url, headers=headers, files=files)
    transient_doc_id = json.loads(response.content)["transientDocumentId"]
    
    # Enviar para assinatura
    agreement_url = "https://api.echosign.com/api/rest/v6/agreements"
    payload = {
        "fileInfos": [
            {
                "transientDocumentId": transient_doc_id
            }
        ],
        "recipients": [
            {
                "email": recipient_email
            }
        ]
    }
    headers = {
        "Access-Token": access_token,
        "Content-Type": "application/json"
    }
    response = requests.post(agreement_url, headers=headers, data=json.dumps(payload))
    agreement_id = json.loads(response.content)["id"]
    return agreement_id

# Exemplo de uso
file_path = "caminho_para_seu_arquivo.pdf"
recipient_email = "exemplo@email.com"
agreement_id = send_for_signature(file_path, recipient_email)
print("Documento enviado para assinatura. ID do acordo:", agreement_id)

```

## Adobe Acrobat Sign
![Imagem](https://www.adobe.com/content/dam/dx-dc/us/en/sign…olution/DC_E-sign_Test_Desktop_Loupe3.png.img.png)
