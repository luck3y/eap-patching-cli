EAP S2I One-off Patch Example:
===============

Basic instuctions: 

- Create ConfigMap with patch:
   $ oc create -n myproject configmap jbeap-16108.zip --from-file=jbeap-16108.zip

- Update extensions/patch.cli to install the required patch.
   - for multiple patches, you would probably want to create seperate .cli files for each application, and apply them in any required order by modifying extensions/postconfigure.sh

- Make sure that the project has .s2i/environment with the following contents:
    CUSTOM_INSTALL_DIRECTORIES=extensions
  (Alternatively, you can pass this to oc new-app with -e CUSTOM_INSTALL_DIRECTORIES)

- You can now either modify your existing deployment config to mount the configmap containing the patch, or modify the application template to provide the required mount configuration, see: https://github.com/luck3y/hello-world-war/blob/patching/eap71-basic-s2i-patching.json#L344 and https://github.com/luck3y/hello-world-war/blob/patching/eap71-basic-s2i-patching.json#L433 for the required mount configuration. The volume name should match the configmap created in step 1 (jbeap-16108.zip, in this example.) 

- install / replace the template: 
    $ oc -n openshift replace --force -f eap71-basic-s2i-patching.json

- recreate your application:
    $ oc new-app --template=eap71-basic-s2i-patching \
       -p SOURCE_REPOSITORY_URL="https://github.com/luck3y/hello-world-war.git" \
       -p SOURCE_REPOSITORY_REF="patching" \
       -p CONTEXT_DIR="" \
       -p APPLICATION_NAME="eap-patching-demo" 

