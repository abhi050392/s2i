Projects
--------
`$ oc new-project cicd`

`$ oc new-project dev-fis`

`$ oc new-project dev-web`

`$ oc new-project test`

`$ oc new-project prod`

Jenkins Setup
-------------
- [Spawn Jenkins / Nexus in cicd](https://github.com/benemon/docker-nexus/README.md)

### Give Jenkins rights to build stuff in each namespace
`$ oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n dev-fis`

`$ oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n dev-web`

`$ oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n test`

`$ oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n prod`


### Give each namespace rights to pull stuff from the proceeding namespaces
`$ oc policy add-role-to-group system:image-puller system:serviceaccounts:test -n dev-fis`

`$ oc policy add-role-to-group system:image-puller system:serviceaccounts:test -n dev-web`

`$ oc policy add-role-to-group system:image-puller system:serviceaccounts:prod -n dev-fis`

`$ oc policy add-role-to-group system:image-puller system:serviceaccounts:prod -n dev-web`

### Check the bindings on each namespace
`$ oc describe policyBindings :default`

### Create the template objects in dev for FIS
`$ oc project dev-fis`

`$ oc create -f https://github.com/benemon/multi-module-s2i-project/blob/jenkins/openshift/multi-module-dev.yaml`

`$ oc new-app --template=multi-module-example`
 
### Create the template objects in dev for WEB
`$ oc project dev-web`

`$ oc create -f https://github.com/benemon/multi-module-s2i-project/blob/jenkins/openshift/multi-module-dev.yaml`

`$ oc new-app --template=multi-module-example`


### Create the pipleines in cicd
`$ oc project cicd`

`$ oc create -f https://raw.githubusercontent.com/benemon/multi-module-s2i-project/jenkins/fis/pipeline/pipeline.yml`

`$ oc create -f https://raw.githubusercontent.com/benemon/multi-module-s2i-project/jenkins/web/pipeline/pipeline.yml`

### Create DC in Test
`$ oc create deploymentconfig country-service --image=<IP>:5000/dev-fis/country-service:test`

`$ oc create deploymentconfig country-web --image=<IP>:5000/dev-web/country-web:test`

### Create DC in Prod
`$ oc create deploymentconfig country-service --image=<IP>:5000/dev-fis/country-service:prod`

`$ oc create deploymentconfig country-web --image=<IP>:5000/dev-web/country-web:prod`

`$ oc expose dc country-service --port=8080`

`$ oc expose svc country-service`

`$ oc expose dc country-web --port=8080`

`$ oc expose svc country-web`

