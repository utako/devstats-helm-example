# devstats-helm-example

DevStats Deployment on Kubernetes using Helm. This is an example deployment of a single project - CNCF organization.

Helm chart in `devstats-helm-example`.


# Usage

You should set namespace to 'devstats' first: `./switch_namespace.sh devstats`.

Please provide secret values for each file in `./secrets/*.secret.example` saving it as `./secrets/*.secret` or specify them from the command line.

Please note that `vi` automatically adds new line to all text files, to remove it run `truncate -s -1` on a saved file.

List of secrets:
- File `secrets/PG_ADMIN_USER.secret` or --set `pgAdminUser=...` setup postgres admin user name.
- File `secrets/PG_HOST.secret` or --set `pgHost=...` setup postgres host name.
- File `secrets/PG_PORT.secret` or --set `pgPort=...` setup postgres port.
- File `secrets/PG_PASS.secret` or --set `pgPass=...` setup postgres password for the default user (gha_admin).
- File `secrets/PG_PASS_RO.secret` or --set `pgPassRO=...` setup for the read-only user (ro_user).
- File `secrets/PG_PASS_TEAM.secret` or --set `pgPassTeam=...` setup the team user (also read-only) (devstats_team).
- File `secrets/GHA2DB_GITHUB_OAUTH.secret` or --set `githubOAuth=...` setup GitHub OAuth token(s) (single value or comma separated list of tokens).
- File `secrets/GF_SECURITY_ADMIN_USER.secret` or --set `grafanaUser=...` setup Grafana admin user name.
- File `secrets/GF_SECURITY_ADMIN_PASSWORD.secret` or --set `grafanaPassword=...` setup Grafana admin user password.

You can select which secret(s) should be skipped via: `--set skipPGSecret=1,skipGitHubSecret=1,skipGrafanaSecret=1`.

You can install only selected templates, see `values.yaml` for detalis (refer to `skipXYZ` variables in comments), example:
- `helm install --dry-run --debug ./devstats-helm-example --set skipSecrets=1,skipPVs=1,skipBootstrap=1,skipProvisions=1,skipCrons=1,skipGrafanas=1,skipServices=1`.

You can restrict ranges of projects provisioned and/or range of cron jobs to create via:
- `--set indexPVsFrom=5,indexPVsTo=9,indexProvisionsFrom=5,indexProvisionsTo=9,indexCronsFrom=5,indexCronsTo=9,indexGrafanasFrom=5,indexGrafanasTo=9,indexServicesFrom=5,indexServicesTo=9`.

You can overwrite the number of CPUs autodetected in each pod, setting this to 1 will make each pod single-threaded
- `--set nCPUs=1`.

Please note variables commented out in `./devstats-helm-example/values.yaml`. You can either uncomment them or pass their values via `--set variable=name`.

# EKS cluster

If you want to use EKS cluster, there are some shell scripts in `scripts` directory that can be useful:

- `cncfekscluster.sh` - can be used to create EKS cluster, it uses [eksctl](https://eksctl.io).
- `cncfkubectl.sh` - once cluster is up and running you can use it as `kubectl` - it is configured to use cluster created by `cncfekscluster.sh`.
- `cncfec2desc.sh` - you can use it to list `EC2` instances created by `cncfekscluster.sh`.

Before using any of those script you need to define `cncf` AWS profile by modifying files in `~/.aws/` directory:

- `config` (example):
```
[profile cncf]
region = eu-west-3
output = json
```
- `credentials` (example redacted):
```
[cncf]
aws_secret_access_key = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
aws_access_key_id = yyyyyyyyyyyyyyyyyy
```
