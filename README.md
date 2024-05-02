# Helm Chart Deployment

## How to edit Helm Chart to new version.
- Edit Deployment, Service, HPA, ServiceAccount or new configuration template in [templates/](templates/)
- After edit change [Chart.yaml](./Chart.yaml#L18) to new version
```
version: 0.1.1 > to new version
```
- Build Helm Chart package. After you build, you will see file name "Chart Name-version.tgz"
```
helm package .
```
- Push to Azure ACR
```
helm registry login <acr url> --username <username> --password <password>
helm push <chartname.tgz> <acr url> --username <username> --password <password>
```
- Push to Gitlab Package Registry
    - Create a Deploy Token via Settings > Repositoty > Deploy tokens and scope set to ```read_package_registry```, ```write_package_registry```, or both 
    - Install helm plugin ```cm-push``` on Linux or WSL or MacOS machine
    ```sh
    helm plugin install https://github.com/chartmuseum/helm-push
    ```
    - Add Gitlab Package Registry as Helm repo
    ```sh
    helm repo add --username <username> --password <access_token> <Chart Name> https://gitlab.com/api/v4/projects/<project_id>/packages/helm/stable
    ```
    - Push Helm package to Gitlab Package Registry 
    ```sh
    helm cm-push <chartname.tgz> <Chart Name>
- Install Helm Chart
    - Helm Chart stored in Azure ACR
    ```
    helm install <Release name> oci://<acr-url>/<acr repository> --version <Version of helm chart (optional)> -f <values.yaml file>
    ```
    - Helm Chart stored in Gitlab Package Registry
    ```
    helm install <Release name> <Chart Name/Sub-Chart Name> -n <Namespace> -f <values.yaml>
    ```
- Push configurations to repositiry and create tag with Helm Chart version
```sh
git push origin main
git tag <Helm Chart version>
git push origin --tags
```