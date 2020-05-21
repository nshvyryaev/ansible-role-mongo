# ansible-role-mongo  [![Build Status](https://travis-ci.com/nshvyryaev/ansible-role-mongo.svg?branch=master)](https://travis-ci.com/nshvyryaev/ansible-role-mongo)

Ansible role for installing MongoDB.
Molecule tests TraviCI driven in GCE

## How to integrate Molecule with GCE

  - **Ssh key**

    - Generate ssh key in advance
        ```
        ssh-keygen -t rsa -f google_compute_engine -C 'travis' -q -N ''
        ```
    - Then, add it to GCP project metadata manually

  - **GCP Service Account**

    - In GCP Console go to IAM - Service Accounts and create a new one with following roles:
      - Compute Engine - Compute Instance Admin (v1)
      - Service Account User

    - Download json key and save it to file `credentials.json`

  - **TravisCI**

    - Go to https://travis-ci.com/ and authorize it to access this repo

      **Important**
      Limit max concurrent builds for the repo to 1 because each build triggers Molecule tests
      with single GCE project as environment!

    - In locally cloned repo authenticate to Travis (it should be preinstalled):
      ```
      travis login --com
      ```
    - Encrypt secrets:
       ```
       travis encrypt GCE_SERVICE_ACCOUNT_EMAIL='service-account-user-name@infra-NNNNNN.iam.gserviceaccount.com' --add env.global --com
       travis encrypt GCE_CREDENTIALS_FILE="$(pwd)/credentials.json" --add env.global --com
       travis encrypt GCE_PROJECT_ID='infra-NNNNNN' --add env.global --com
       ```
    - Encrypt files:
      ```
      tar cvf secrets.tar credentials.json google_compute_engine
      travis login
      travis encrypt-file secrets.tar --add --com
      ```
