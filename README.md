EAP S2I One-off Patch Example:
===============

Note, this example illustrates a means to apply a one-off patch to EAP, for more complicated patching, the source image should be built with the patches already applied.

Basic instuctions: 

- Create a ConfigMap with patch:

  ```$ oc create -n myproject configmap jbeap-16108.zip --from-file=jbeap-16108.zip```

- Update extensions/patch.cli to install the required patch.
   - for multiple patches, seperate .cli files for each patch application could be created, and applied in any required order from extensions/postconfigure.sh

- The project should have a .s2i/environment with the following contents:
    
    ```CUSTOM_INSTALL_DIRECTORIES=extensions```
  
  (Alternatively, this may be provided to oc new-app with -e CUSTOM_INSTALL_DIRECTORIES)

- The application template or existing deployment config may now be modified to mount the configmap containing the patch and the application rebuilt, see: https://github.com/luck3y/hello-world-war/blob/patching/eap71-basic-s2i-patching.json#L344 and https://github.com/luck3y/hello-world-war/blob/patching/eap71-basic-s2i-patching.json#L433 for the required mount configuration. The volume name should match the configmap created in step 1 (jbeap-16108.zip, in this example.) The modifications to the deployment controller are similar (use ```oc edit dc/eap-app-name``` for the deployment controller approach, or see below to modify the template in use).

- install / replace the template: 
    $ oc -n openshift replace --force -f eap71-basic-s2i-patching.json

- create / recreate the application:
    $ oc new-app --template=eap71-basic-s2i-patching \
       -p SOURCE_REPOSITORY_URL="https://github.com/luck3y/hello-world-war.git" \
       -p SOURCE_REPOSITORY_REF="patching" \
       -p CONTEXT_DIR="" \
       -p APPLICATION_NAME="eap-patching-demo" 

