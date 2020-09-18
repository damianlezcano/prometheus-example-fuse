# prometheus-example-fuse

#### app-project1 (build jenkins strategy)

Configuramos credenciales para que jenkins acceda al repositorio
    
    oc new-project app-project1

    oc create secret generic repository-credentials --from-literal=username=${GITHUB_USER} --from-literal=password=${GITHUB_TOKEN} --type=kubernetes.io/basic-auth -n app-project1

    oc label secret repository-credentials credential.sync.jenkins.openshift.io=true -n app-project1
    
    oc annotate secret repository-credentials 'build.openshift.io/source-secret-match-uri-1=ssh://github.com/*' -n app-project1

    oc adm policy add-role-to-user admin system:serviceaccount:jenkins:jenkins -n app-project1

Desplegamos app example1

    oc create -f https://raw.githubusercontent.com/damianlezcano/prometheus-example-fuse/master/template.yaml -n app-project1

    oc new-app --template java-app-deploy -p APP_NAME=example1 -p GIT_REPO=https://github.com/damianlezcano/prometheus-example-fuse.git -p GIT_BRANCH=edenor -n app-project1

    oc start-build example1-pipeline -n app-project1

Generar commit para el tablero DevOps (MDT)

    git clone https://github.com/damianlezcano/prometheus-example-fuse.git -b edenor
    cd prometheus-example-fuse
    
    date > version.txt
    
    git add .;git commit -m "Cambio en version";git push

    oc start-build example1-pipeline -n app-project1

_Si se reemplaza 'world' por 'error' esto genera un error en la ruta camel enviando el mensaje a la DLQ, permitiendo activar luego de mas de 10 intentos una de las reglas configuradas en prometheus y generando finalmente una notificacion por email.-
