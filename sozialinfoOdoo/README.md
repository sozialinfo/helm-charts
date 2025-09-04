# Mint System Odoo

This Helm chart deploys Odoo with PostgreSQL.

## Installation Instructions

### 1. Create a GitHub Credentials Secret

You must create a Kubernetes secret containing both your GitHub username and your GitHub Personal Access Token. This secret is required for downloading private Odoo Enterprise or private addons.

Create the secret using the following command:

```sh
kubectl create secret generic odoo-github \
    --from-literal=GITHUB_USERNAME=<YOUR_GITHUB_USERNAME> \
    --from-literal=GITHUB_PERSONAL_ACCESS_TOKEN=<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN> \
    -n <namespace>
```

Replace `<YOUR_GITHUB_USERNAME>` and `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>` with your actual credentials and `<namespace>` with the namespace where you are deploying Odoo.

### 2. Add DockerHub Registry Credentials Secret

If you use a private DockerHub registry, create a Kubernetes secret for your DockerHub credentials:

```sh
kubectl create secret docker-registry dockerhub-registry-read \
    --namespace=<your-namespace> \
    --docker-server=https://index.docker.io/v1/ \
    --docker-username=<your-dockerhub-username> \
    --docker-password=<your-dockerhub-access-token>
```

Replace `<your-namespace>`, `<your-dockerhub-username>`, and `<your-dockerhub-access-token>` with your actual DockerHub namespace, username, and access token.

### 3. Add Uploadcare Secret
```sh
kubectl create secret generic odoo-uploadcare-secret \
    --from-literal=public-key=<UPLOADCARE_PUBLIC_KEY> \
    --from-literal=secret-key=<UPLOADCARE_SECRET_KEY> \
    -n <namespace>
```

### 4. Install the Chart

Install the chart using Helm:

```sh
helm upgrade --install sozialinfo . -f values.yaml
```

## Configuration

### External Services Integration

The chart includes support for several external services through environment variables:

- **Meilisearch**: For enhanced search functionality (`odoo-meilisearch-secret`)
- **Uploadcare**: For file upload and management services (`odoo-uploadcare-secret`)
- **Netlify**: For static site deployments (`odoo-netlify-secret`)
- **Keycloak**: For OAuth authentication (`odoo-keycloak-secret`)

Each service uses its own dedicated Kubernetes secret, which are referenced through the `extraEnv` section in values.yaml. You can customize which services to include by modifying the `extraEnv` configuration in your values.yaml file.

### Adding Custom Environment Variables

To add additional environment variables, update the `extraEnv` section in your values.yaml:

```yaml
extraEnv: |
  - name: MY_CUSTOM_VAR
    value: "my-custom-value"
  - name: MY_SECRET_VAR
    valueFrom:
      secretKeyRef:
        name: my-custom-secret
        key: secret-key
```

## Parameters

### Ingress parameters

| Name                       | Description                                  | Value            |
| -------------------------- | -------------------------------------------- | ---------------- |
| `ingress.enabled`          | Enable or disable the ingress                | `true`           |
| `ingress.className`        | The class name for the ingress               | `nginx`          |
| `ingress.clusterIssuerRef` | The cluster issuer reference for the ingress | `nil`            |
| `ingress.host`             | The host for the ingress                     | `odoo.knd.local` |
| `ingress.customDomain`     | The custom domain for the ingress            | `""`             |

### vshnPostgres parameters

| Name                          | Description                               | Value             |
| ----------------------------- | ----------------------------------------- | ----------------- |
| `vshnPostgres.enabled`        | Enable or disable vshnPostgres            | `false`           |
| `vshnPostgres.secretRef`      | The secret reference for vshnPostgres     | `odoo-postgresql` |
| `vshnPostgres.client.enabled` | Enable or disable the vshnPostgres client | `false`           |

### Postgres parameters

| Name                        | Description                       | Value           |
| --------------------------- | --------------------------------- | --------------- |
| `postgres.enabled`          | Enable or disable Postgres        | `false`         |
| `postgres.db`               | The database name for Postgres    | `odoo`          |
| `postgres.user`             | The username for Postgres         | `odoo`          |
| `postgres.secretRef`        | The secret reference for Postgres | `odoo-postgres` |
| `postgres.storageClassName` | Set the storage class             | `standard`      |

### PostgreSQL parameters

| Name                                          | Description                                     | Value               |
| --------------------------------------------- | ----------------------------------------------- | ------------------- |
| `postgresql.enabled`                          | Enable or disable PostgreSQL                    | `false`             |
| `postgresql.auth.username`                    | The username for PostgreSQL authentication      | `odoo`              |
| `postgresql.auth.database`                    | The database name for PostgreSQL authentication | `odoo`              |
| `postgresql.auth.existingSecret`              | Name of the secret key.                         | `odoo-postgresql`   |
| `postgresql.auth.secretKeys.adminPasswordKey` | The admin password key for PostgreSQL           | `postgres-password` |
| `postgresql.auth.secretKeys.userPasswordKey`  | The user password key for PostgreSQL            | `password`          |

### CloudNativePG parameters

| Name                        | Description                                                             | Value  |
| --------------------------- | ----------------------------------------------------------------------- | ------ |
| `cnpg.cluster.enabled`      | Enable or disable CloudNativePG                                         | `true` |
| `cnpg.cluster.nameOverride` | Override the name of the PostgreSQL cluster                             | `""`   |
| `cnpg.cluster.instances`    | Number of PostgreSQL instances (1 for single, 2+ for high availability) | `1`    |
| `cnpg.cluster.storage.size` | Persistent volume size for each instance                                | `8Gi`  |
| `cnpg.database.enabled`     | Enable or disable creation of a Database resource                       | `true` |
| `cnpg.database.name`        | Name of the PostgreSQL database to create                               | `odoo` |
| `cnpg.database.user`        | Name of the database user (optional, defaults to database name)         | `odoo` |
| `cnpg.database.secretName`  | Name of the secret to store credentials                                 | `""`   |

### Odoo parameters

| Name                        | Description                                   | Value                                                                                                                                                         |
| --------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enabled`                   | Enable or disable odoo                        | `true`                                                                                                                                                        |
| `image`                     | The image for odoo                            | `mintsystem/odoo:18.0.20250725`                                                                                                                               |
| `imagePullPolicy`           | Pull policy for odoo image                    | `Always`                                                                                                                                                      |
| `proxyMode`                 | Enable or disable proxy mode for odoo         | `true`                                                                                                                                                        |
| `githubUsername`            | The GitHub username for odoo                  | `""`                                                                                                                                                          |
| `githubPersonalAccessToken` | The GitHub personal access token for odoo     | `""`                                                                                                                                                          |
| `downloadOdooEnterprise`    | Enable or disable downloading Odoo Enterprise | `false`                                                                                                                                                       |
| `addonsGitRepos`            | List of addon Git repositories for odoo       | `["https://github.com/Mint-System/Odoo-Apps-Server-Tools.git#18.0","https://github.com/OCA/Server-Tools.git#18.0","https://github.com/OCA/Project.git#18.0"]` |
| `database`                  | The database for odoo                         | `odoo`                                                                                                                                                        |
| `initLang`                  | The initial language for odoo                 | `de_CH`                                                                                                                                                       |
| `listDB`                    | Enable or disable listing databases for odoo  | `false`                                                                                                                                                       |
| `secretRef`                 | The secret reference for odoo                 | `odoo-creds`                                                                                                                                                  |

### K8up parameters

| Name           | Description            | Value   |
| -------------- | ---------------------- | ------- |
| `k8up.enabled` | Enable or disable K8up | `false` |
