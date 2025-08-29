# Lampac Helm Repository

This is the official Helm chart repository for Lampac - a torrent streaming service.

## Usage

[Helm](https://helm.sh) must be installed to use the charts. Please refer to Helm's [documentation](https://helm.sh/docs) to get started.

Once Helm has been set up correctly, add the repo as follows:

```bash
helm repo add lampac https://vizzletf.github.io/lampac_helm
```

If you had already added this repo earlier, run `helm repo update` to retrieve the latest versions of the packages.

You can then run `helm search repo lampac` to see the charts.

## Installing the Lampac Chart

To install the lampac chart:

```bash
helm install my-lampac lampac/lampac --namespace lampac --create-namespace
```

To uninstall the chart:

```bash
helm uninstall my-lampac --namespace lampac
```

## Configuration

For configuration options, please see the [main repository](https://github.com/VizzleTF/lampac_helm) documentation.

## Source

- Chart Source: https://github.com/VizzleTF/lampac_helm
- Application Source: https://github.com/immisterio/Lampac