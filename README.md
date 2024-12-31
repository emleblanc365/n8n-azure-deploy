# n8n Azure Deployment Templates

Questo repository contiene due template ARM per deployare e gestire n8n su Azure Container Instances (ACI) con storage persistente usando Azure File Share.

## Deploy Rapido

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdanilozito%2Fn8n-azure-deploy%2Fmain%2Fdeploy-n8n.json)

[![Update in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdanilozito%2Fn8n-azure-deploy%2Fmain%2Fupdate-n8n.json)

## Configurazione Predefinita

I template includono valori predefiniti per un deployment rapido:

- `baseName`: "n8n"
- `n8nUsername`: "admin"
- `n8nPassword`: "n8n-default-pwd"
- `location`: "westeurope"
- `n8nImageVersion`: "latest" (solo per update-n8n.json)

⚠️ **IMPORTANTE**: Per un ambiente di produzione, si raccomanda vivamente di modificare questi valori, specialmente username e password.

## Personalizzazione dei Parametri

### Tramite Portal Azure
1. Clicca sul pulsante "Deploy to Azure" sopra
2. Compila i parametri nel form che appare
3. Rivedi e crea il deployment

### Tramite CLI Azure

#### Nuovo Deployment
```bash
az deployment group create \
  --resource-group <RESOURCE_GROUP_NAME> \
  --template-file deploy-n8n.json \
  --parameters \
      baseName=<BASE_NAME> \
      n8nUsername=<USERNAME> \
      n8nPassword=<PASSWORD> \
      location=<LOCATION>
```

#### Aggiornamento
```bash
az deployment group create \
  --resource-group <RESOURCE_GROUP_NAME> \
  --template-file update-n8n.json \
  --parameters \
      baseName=<BASE_NAME> \
      n8nUsername=<USERNAME> \
      n8nPassword=<PASSWORD> \
      location=<LOCATION> \
      n8nImageVersion=<VERSION>
```

## Note sulla Sicurezza

- Cambiare sempre le credenziali predefinite prima del deployment in produzione
- Utilizzare password complesse per l'autenticazione
- Considerare l'implementazione di misure di sicurezza aggiuntive come:
  - Restrizioni IP
  - VNet integration
  - Managed identities

## Accesso all'Applicazione

Dopo il deployment, n8n sarà accessibile all'indirizzo:
```
http://<baseName>-dns.<location>.azurecontainer.io:5678
```

## Troubleshooting

- Se il container non si avvia, controllare i log nel portal Azure
- Verificare che il storage account sia accessibile
- Controllare che le credenziali di autenticazione siano corrette
