#DO288 Cheat Sheet
## Basics
Login: oc login -u #USER# -p #PASSWORD# OC_URL
New Project: oc new-project XYZ
Switch Project: oc project XYZ
Status: oc status
View logs: oc logs -f TYPE/NAME (bc -> build config, dc -> deployment config)
List pods: oc get pod
List routes: oc get route
List endpoints: oc get endpoints
Remote Shell: oc rsh POD-1-1bdsef COMMAND
Set env vars: oc set env dc/NAME --prefix=PREFIX --from=cm/CONFIGMAP (or secret(NAME)
List env vars: oc set env dc/NAME --list
Create Config map: oc create cm NAME --from-literal param=value --fro...
Create Secrect: oc create secret NAME --from-literal param=value --fro...

## GIT Basics
git add NAME
git commit -a -m 'COMMENT'
git push
git checkout master
git checkout -b BRANCH
git push -u origin BRANCH

## Deployment Config
### Create
oc new-app --as-deployment-config --name NAME --docker-image docker_IMAGE_URL -e VAR=value -i PROJECT/IMAGE_STREAM_NAME
### Rollout new Deployment
oc rollout latest dc/NAME
### Rollback
oc rollback dc/NAME

## Openshift Template
### Download a Template
oc get template nodejs-mongodb-example -n openshift \ -o yaml
### Check Params
oc process --parameters -f template.yaml
### Create app from template
Dry run: oc new-app --dry-run -f template.yaml -p param=xyc
Create: oc new-app PROJECT/TEMPLATE-RSC -p param=xyz
### Create Template Resource
oc create -f template.yaml

## Permissions
### Grant permission to an imagestream of a different project
oc policy add-role-to-group -n DEST_PROJECT system:image-puller system:serviceaccounts:ACCESSOR_PROJECT
### Jenkins
oc policy add-role-to-user edit system:serviceaccount:JENKINS-PROJECT:jenkins -n PROJECT

## Network
### Port Forward
oc port-forward POD-1-dvcqm 30306:3306 (from:to)
### Expose Service
oc expose svc SERVICENAME

## S2I
### Folders
.s2i/bin/assemble

### Folders from image stream
oc describe istag nodejs:12 -n openshift | grep io.openshift.s2i.scripts-url

## Image Strean
### Create from Image Repository
oc import-image NAME --confirm --reference-policy local --from quay.io/USER/NAME
### Login into QUAY.io
podman login -u USER quay.io
oc create secret generic quayio --from-file .dockerconfigjson=${XDG_RUNTIME_DIR}/containers/auth.json --type kubernetes.io/dockerconfigjson
### Build Image locally
sudo podman build -t NAME . (dir)
### Push Image to repository
sudo skopeo copy containers-storage:localhost/NAME docker://quay.io/USER/NAME
