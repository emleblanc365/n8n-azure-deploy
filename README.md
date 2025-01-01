# n8n Azure Deployment Template

Questo template ARM permette di deployare n8n su Azure Container Instances (ACI) con storage persistente usando Azure File Share.

## Prerequisiti

- **Account Azure** con una sottoscrizione attiva
- **Account Docker Hub**: A causa dei limiti di rate di Docker Hub, è necessario [registrare un account gratuito](https://hub.docker.com/signup) per ottenere le credenziali di accesso. Questo eviterà errori durante il download delle immagini dei container.

## Componenti del Deployment

- **n8n**: Container principale che esegue l'applicazione n8n
- **Caddy**: Reverse proxy che fornisce:
  - Gestione automatica dei certificati SSL/TLS
  - Routing HTTPS sicuro
  - Reindirizzamento automatico HTTP a HTTPS
- **Storage Persistente**: Azure File Share per il database SQLite di n8n

## Deploy Rapido

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdanilozito%2Fn8n-azure-deploy%2Fmain%2Fdeploy-n8n.json)

## Configurazione Predefinita

Il template include valori predefiniti per un deployment rapido:

- `baseName`: "n8n"
- `n8nUsername`: "admin"
- `n8nPassword`: "n8n-default-pwd"
- `location`: "westeurope"
- `fileShareQuotaGB`: 5
- `dockerHubUsername`: "" (inserire il proprio username Docker Hub)
- `dockerHubPassword`: "" (inserire la propria password Docker Hub)

⚠️ **IMPORTANTE**: 
- Per un ambiente di produzione, si raccomanda vivamente di modificare username e password di n8n
- È necessario inserire le credenziali Docker Hub per evitare errori di rate limiting durante il deployment
- I dati di n8n vengono persistiti nell'Azure File Share, assicurandosi che nessun dato venga perso in caso di restart del container

## Caratteristiche di Sicurezza

Il template implementa diverse misure di sicurezza:

- **Storage Account**:
  - TLS 1.2 forzato
  - HTTPS-only
  - Encryption abilitata per i file

- **Container**:
  - Resource limits configurati
  - Health probes per il monitoring
  - Autenticazione basic auth abilitata

- **Caddy**:
  - Gestione automatica SSL/TLS
  - Redirect HTTP a HTTPS

## Personalizzazione dei Parametri

### Tramite Portal Azure
1. Clicca sul pulsante "Deploy to Azure" sopra
2. Compila i parametri nel form che appare:
   - Seleziona o crea un nuovo Resource Group
   - Inserisci le credenziali Docker Hub
   - Modifica gli altri parametri se necessario
3. Rivedi e crea il deployment

### Tramite CLI Azure

```bash
az deployment group create \
  --resource-group <RESOURCE_GROUP_NAME> \
  --template-file deploy-n8n.json \
  --parameters \
      baseName=<BASE_NAME> \
      n8nUsername=<USERNAME> \
      n8nPassword=<PASSWORD> \
      location=<LOCATION> \
      dockerHubUsername=<DOCKER_USERNAME> \
      dockerHubPassword=<DOCKER_PASSWORD>
```

## Accesso all'Applicazione

Dopo il deployment, n8n sarà accessibile all'indirizzo:
```
https://<baseName>-dns.<location>.azurecontainer.io
```

## Troubleshooting

- Se il container non si avvia, controllare i log nel portal Azure
- Verificare che le credenziali Docker Hub siano corrette
- Controllare che le credenziali di autenticazione n8n siano corrette
- Verificare che il storage account sia accessibile
