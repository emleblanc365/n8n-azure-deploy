# n8n Azure Deployment Templates

Questo repository contiene due template ARM per deployare e gestire n8n su Azure Container Instances (ACI) con storage persistente usando Azure File Share.

## Componenti del Deployment

- **n8n**: Container principale che esegue l'applicazione n8n
- **Caddy**: Reverse proxy che fornisce:
  - Gestione automatica dei certificati SSL/TLS
  - Routing HTTPS sicuro
  - Reindirizzamento automatico HTTP a HTTPS

## Deploy Rapido

### Nuovo Deployment (Prima Installazione)
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdanilozito%2Fn8n-azure-deploy%2Fmain%2Fdeploy-n8n.json)

Usa questo pulsante per la prima installazione di n8n. Creerà tutte le risorse necessarie incluso storage account e file share.

### Aggiornamento Installazione Esistente
[![Update in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdanilozito%2Fn8n-azure-deploy%2Fmain%2Fupdate-n8n.json)

Usa questo pulsante per aggiornare un'installazione esistente di n8n, per esempio per cambiare la versione dell'immagine o le credenziali.

## Configurazione Predefinita

I template includono valori predefiniti per un deployment rapido:

- `baseName`: "n8n"
- `n8nUsername`: "admin"
- `n8nPassword`: "n8n-default-pwd"
- `location`: "westeurope"
- `fileShareQuotaGB`: 5
- `n8nImageVersion`: "latest" (solo per update-n8n.json)

⚠️ **IMPORTANTE**: Per un ambiente di produzione, si raccomanda vivamente di modificare questi valori, specialmente username e password.

## Caratteristiche di Sicurezza

I template implementano diverse misure di sicurezza:

- **Storage Account**:
  - TLS 1.2 forzato
  - HTTPS-only
  - Encryption abilitata per i file

- **Container**:
  - Resource limits configurati
  - Health probes per il monitoring
  - Headers di sicurezza HTTP configurati

- **Caddy**:
  - Gestione automatica SSL/TLS
  - Security headers (HSTS, X-Frame-Options, etc.)
  - Redirect HTTP a HTTPS

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

## Architettura del Deployment

Il deployment crea:
1. Un container group con due container:
   - **n8n**: Espone l'applicazione sulla porta 5678
   - **Caddy**: Gestisce il traffico HTTPS e i certificati SSL
2. Storage persistente per i dati di n8n
3. Configurazione di rete con DNS pubblico

## Note sulla Sicurezza

- Cambiare sempre le credenziali predefinite prima del deployment in produzione
- Utilizzare password complesse per l'autenticazione
- Il traffico HTTPS è gestito automaticamente da Caddy
- Considerare l'implementazione di misure di sicurezza aggiuntive come:
  - Restrizioni IP
  - VNet integration
  - Managed identities

## Accesso all'Applicazione

Dopo il deployment, n8n sarà accessibile all'indirizzo:
```
https://<baseName>-dns.<location>.azurecontainer.io
```
L'accesso HTTPS è configurato automaticamente da Caddy.

## Troubleshooting

- Se il container non si avvia, controllare i log nel portal Azure
- Verificare che il storage account sia accessibile
- Controllare che le credenziali di autenticazione siano corrette
- Per problemi SSL, verificare i log del container Caddy
