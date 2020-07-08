# W906 LB02

## VM
IP: 192.168.152.14
OS: Ubuntu Server 20.04

## Kanboard
Läuft via Kubernetes.
Erreichbar via 192.168.152.14:32200

## Theia
Läuft via Kubernetes.
Erreichbar via 192.168.152.14:32400

## Jenkins
Läuft via Kubernetes.
Erreichbar via 192.168.152.14:32100

## Pipeline
Via Jenkins erstellt bei einem neuen Build einen neuen Release auf GitHub erstellt und ein neues Projekt im Kanboard angelegt.

## K1
Als Planungstool wird Kanboard verwendet.
Kanboard kann man mit dem folgendem Command starten:
```bash
kubectl apply -f W906/kanboard.yaml
```
Kanboard ist mit Jenkins in der Pipeline verknüpft.

## K2
Als Versionsverwaltung wird Git, resp GitHub verwendet. Unser Fortschritt ist auf github.com/m-vog/w906 verfügbar.
Bei allen Gruppenmitglieder ist das Repository eingrichtet. Die Gruppenmitglieder sind als Maintainer berechtigt.

GitHub ist mit Jenkins in der Pipeline verknüpft.

## K3
Als Entwicklungsumbegung verwenden wir lokal installiert VSCode. Theia ist auch Verfügbar.
Die Tools sind via Git in der Pipeline verknüpft.

Theia kann man wie folgt starten:
```bash
kubectl apply -f W906/theia.yaml
```

## K4
Als Build-Software wird eine Mischung aus GitHub und DockerHub verwendet.
Auf unserem Repository haben wir ein Dockerfile abgelegt. Das Repository haben wir mit unserem DockerHub Repository verknüft. Das heisst; wenn ein neuer Commit in das GitHub Repo gepushed wird, triggered DockerHub einen automatischen Build von unserem Dockerfile.

![DockerHub build](https://github.com/m-vog/W906/blob/master/img/dockerhub.PNG)
![DockerHub build](https://github.com/m-vog/W906/blob/master/img/docker_success.PNG)

Zusätzlich kann man über DockerHub direkt einen Test starten

## K5
Für CI verwenden wir code-inspector.com Man muss auf GitHub zwei API Keys von code-inspector hinterlegen und ein Config-File unter

 ```ruby
.github/workflows/main.yaml
```
anlegen. Mit folgendem Inhalt:
```bash
on: [push]

jobs:
  check-quality:
    runs-on: ubuntu-latest
    name: A job to check my code quality
    steps:
    - name: Check code meets quality standards
      id: code-inspector
      uses: codeinspectorio/github-action@master
      with:
        repo_token: ${{ secrets.GITHUB_TOKEN }}
        code_inspector_access_key: ${{ secrets.CODE_INSPECTOR_ACCESS_KEY }}
        code_inspector_secret_key: ${{ secrets.CODE_INSPECTOR_SECRET_KEY }}
        min_quality_grade: 'WARNING'
        min_quality_score: '50'
        max_defects_rate: '0.0001'
        max_complex_functions_rate: '0.0001'
        max_long_functions_rate: '0.0001'
        project_name: 'w906'
        max_timeout_sec: '600'
```
Code-Inspector inspiziert unseren Code und bewertet ihn, nach jedem neuen Commit.
![Code Inspector](https://github.com/m-vog/W906/blob/master/img/code_inspector.PNG)


## K6
Als Tool für CD verwenden wir Jenkins. Bei einem neuen Build, triggered Jenkins ein neues Projekt bei unserem Kanboard und einen neuen Release auf unserem GitHub
![Jenkins build](https://github.com/m-vog/W906/blob/master/img/jenkins_build.PNG)

```bash
curl -u "admin:admin" http://192.168.152.14:32200/jsonrpc.php -d '{"jsonrpc": "2.0","method": "createMyPrivateProject","id": 1271584269,"params": ["test01"]}'
curl -u w906-test:w906admin -X POST https://api.github.com/repos/m-vog/W906/releases -d '{ "tag_name": "v0.2.1", "target_commitish": "master", "name": "v1.0.1", "body": "Description of the release", "draft": false,  "prerelease": false }'
```
Wenn ein Problem auftaucht, werden wir benachrichtigt.