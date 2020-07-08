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
Als Tool für CI verwenden wir Jenkins. Bei einem neuen Build, triggered Jenkins ein neues Projekt bei unserem Kanboard und einen neuen Release auf unserem GitHub
![Jenkins build](https://github.com/m-vog/W906/blob/master/img/jenkins_build.PNG)

```bash
curl -u "admin:admin" http://192.168.152.14:32200/jsonrpc.php -d '{"jsonrpc": "2.0","method": "createMyPrivateProject","id": 1271584269,"params": ["test01"]}'
curl -u w906-test:w906admin -X POST https://api.github.com/repos/m-vog/W906/releases -d '{ "tag_name": "v0.2.1", "target_commitish": "master", "name": "v1.0.1", "body": "Description of the release", "draft": false,  "prerelease": false }'
```
## K6
Für CD haben wir Docker Hub verwendet. Wenn ein neuer Commit auf das Github repository gepushed wird, wird ein Build bei DockerHub getriggered.
![DockerHub build](https://github.com/m-vog/W906/blob/master/img/docker_success.PNG)