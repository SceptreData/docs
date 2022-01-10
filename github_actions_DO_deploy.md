# Github Actions: Deploy to github

Github actions spins up a VM on github to do... stuff. It can run
tests, deploy your app and probably do a whole lot more.

Here, I am going to use it to remotely update a laravel application.

## Set up Repo + DO Droplet
Set up your Repo, and then make a `.github/workflows/` folder.
Create your `deploy.yml` file

Set up your droplet, make a deploy account ( or use your own).
Generate an SSH key.
Add the SSH key to your github keys.
Add your public key to your `authorized_keys` on the DO droplet.

Give permission to keys in authorized_leys to perform ssh tasks.
`chmod 700 .ssh/authorized_keys`

## Set up Github Secret
Go to your repo, then your repo's settings.

Settings -> Secrets
Create new Secret (ie SSH_HOST)
Add your servers IP to the secret.

Create a new secret (SSH_KEY) using PRIVATE KEY (AAAAH)
Finally create a secret for the user name  who will be connecting.(SSH_USER)
If necessary:
    Create an SSH_PASSPHRASE secret for your ssh passkey.

## Configure  YAML (Eew) file 
name: simple-ci

on:
  push:
    branches: [main]

jobs:
  pull-on-master:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy Fisheries App to dev server
        uses: appleboy/ssh-action@master
        with:
          host: ${{secrets.SSH_HOST}}
          key: ${{secrets.SSH_KEY}}
          username: ${{secrets.SSH_USER}}
          passphrase: ${{secrets.SSH_PASS}}
          script: |
            cd /var/www/klrd-fisheries-app
            git pull
            composer run-script reset
            echo 'Deployment to DO Dev Droplet successful'

## Commit this script!
Once you commit this to your repo, any pushes to the main branch will trigger
a build.
