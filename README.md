# n8n Azure Deployment Template

Questo template ARM permette di deployare n8n su Azure Container Instances (ACI) con SSL/TLS automatico tramite Caddy e persistenza dei dati tramite Azure File Share.

## Prerequisiti

- **Account Azure** con una sottoscrizione attiva

## Componenti del Deployment

- **n8n**: Container principale che esegue l'applicazione n8n
- **Caddy**: Reverse proxy che fornisce:
  - Gestione automatica dei certificati SSL/TLS
  - Routing HTTPS sicuro
  - Reindirizzamento automatico HTTP a HTTPS
- **Azure File Share**: Storage persistente per i dati di n8n

## Deploy Rapido

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdanilozito%2Fn8n-azure-deploy%2Fmain%2Fdeploy-n8n.json)

## Parametri di Configurazione

Il template richiede i seguenti parametri:

- `name`: Nome del deployment (5-63 caratteri)
  - Deve iniziare con una lettera
  - Può contenere solo lettere minuscole, numeri e trattini
  - Sarà usato per l'URL: `<name>.<region>.azurecontainer.io`
- `storagePrefix`: Prefisso per il nome dello storage account (max 10 caratteri)
  - Valore predefinito: "n8n"
  - Verrà combinato con una stringa univoca
- `n8nUsername`: Username per l'autenticazione di n8n
- `n8nPassword`: Password per l'autenticazione di n8n
- `timeZone`: Timezone per n8n (es: "Europe/Rome")
- `fileShareQuotaGB`: Dimensione in GB del file share per i dati (1-5120)

⚠️ **Note sui nomi**:
- Il nome del deployment (`name`) deve essere lungo almeno 5 caratteri per soddisfare i requisiti DNS di Azure
- Il prefisso dello storage (`storagePrefix`) viene combinato con una stringa univoca per creare un nome storage account valido

## Caratteristiche

- **Persistenza Dati**: Tutti i dati di n8n (workflows, credenziali, etc.) sono salvati in modo persistente su Azure File Share
- **SSL/TLS Automatico**: Caddy gestisce automaticamente i certificati SSL
- **Basic Auth**: Autenticazione integrata per proteggere l'accesso
- **Resource Optimization**: Container configurati con risorse ottimizzate per un uso tipico
- **Auto Restart**: Policy di restart automatico in caso di errori

## Personalizzazione dei Parametri

### Tramite Portal Azure
1. Clicca sul pulsante "Deploy to Azure" sopra
2. Compila i parametri nel form che appare:
   - Seleziona o crea un nuovo Resource Group
   - Inserisci un nome valido per il deployment (min 5 caratteri)
   - Configura username e password per l'accesso a n8n
   - Imposta il timezone desiderato
   - Configura la dimensione del file share se necessario
3. Rivedi e crea il deployment

### Tramite CLI Azure

```bash
az deployment group create \
  --resource-group <RESOURCE_GROUP_NAME> \
  --template-file deploy-n8n.json \
  --parameters \
      name=<NAME> \
      storagePrefix=<STORAGE_PREFIX> \
      n8nUsername=<USERNAME> \
      n8nPassword=<PASSWORD> \
      timeZone=<TIMEZONE> \
      fileShareQuotaGB=<QUOTA_GB>
```

## Accesso all'Applicazione

Dopo il deployment, n8n sarà accessibile all'indirizzo:
```
https://<name>.<region>.azurecontainer.io
```

## Troubleshooting

- Se il container non si avvia, controllare i log nel portal Azure
- Verificare che le credenziali di autenticazione siano corrette
- Verificare che il file share sia stato creato correttamente
- Per problemi SSL, verificare i log del container Caddy
- Se il deployment fallisce per errori di nome:
  - Assicurarsi che il nome del deployment sia almeno 5 caratteri
  - Usare solo lettere minuscole, numeri e trattini
  - Il nome deve iniziare con una lettera
