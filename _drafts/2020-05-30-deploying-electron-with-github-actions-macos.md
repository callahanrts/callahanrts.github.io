---
layout: post
comments: true
title: "Deploying Electron app with Github Actions for macOS"
date: 2020-05-30
categories:
comments: false
---

Figuring out how to build and deploy an electron app is a bit daunting at
first. Fortunately, the process turns out to be fairly simple once you
understand the process and have a few good resources on hand.

The first issue you most likely want to resolve is the unknown developer
privacy alert from apple. In order to do this, you will need to sign your
app.

# [Code Signing](#code-signing)

The code signing process is really simple with
[electron-builder](https://www.electron.build/code-signing). After paying your
$100 toll to Apple, you should be able to create some certificates in XCode. Go
to `Preferences > Accounts > Manage Certificates`. Click the plus in the bottom
left and add each type of certificate. I don't think I needed to download *all*
of them, but I didn't want to miss one and have to come back after I've
forgotten this process.

<center>
<img src="/assets/posts/electron-deploy/xcode-code-sign-certs.png">
</center>

Locally, you should be able to sign your app by running your electron builder
command (`electron-builder`) and your app will be signed. Check the logs to
verify you're not receiving any errors.

To get code signing working in a Github Action, your certificates will need to
be exported. To export your certificates, open keychain and select each
certificate. Right click and export to a `.p12` file. Set a strong password for
the file and save it in your password manager.

<center>
<img src="/assets/posts/electron-deploy/export-code-sign-keys-p12.png">
</center>

The simplest way to import these keys to your Github Action is to store them in
a secrets variable. To do this, go to the settings page of your repository and
click `Secrets` in the side nav. Create a new secret called `CSC_LINK`. The
value of this environment variable is the base64 encoded value of your `.p12`
file.

```bash
# Base64 encode your secrets file
openssl base64 -in apple-certs.p12 -out apple-certs.b64
```

Open the `.b64` file, copy the contents, and paste the result into your
`CSC_LINK` secret on Github.

<center>
<img src="/assets/posts/electron-deploy/base-64-secret-github-actions.png">
</center>

Create another secret called `CSC_KEY_PASSWORD` and use the password you
created as the value. At this point, all the credentials needed to sign your
app are available to your Github Action. The [Github Action](#github-action)
section will show you how to configure them in the action.


# [Notarizing](#notarizing)

# [Github Action](#github-action)

```yaml
name: Build/release

# on: push
on:
  push:
    branches:
      - master

jobs:
  release:
    runs-on: ${{ "{{ matrix.os" }} }}

    strategy:
      matrix:
        os: [macos-latest] #, ubuntu-latest, windows-latest]

    steps:
      - uses: chrislennon/action-aws-cli@v1.1
      - name: Check out Git repository
        uses: actions/checkout@v1

      - name: Install Node.js, NPM and Yarn
        uses: actions/setup-node@v1
        with:
          node-version: 13.6

      - name: Prepare for app notarization
        if: startsWith(matrix.os, 'macos')
        # Import Apple API key for app notarization on macOS
        run: |
          mkdir -p ~/private_keys/
          echo '${{ "{{ secrets.api_key" }} }}' > ~/private_keys/AuthKey_${{ "{{ secrets.api_key_id" }} }}.p8

      - name: Install yarn packages
        run: yarn --cwd workstation install

      - name: Rebuild yarn packages
        run: yarn --cwd workstation rebuild

      - name: Package & Deploy
        env:
          # macOS notarization API key
          appleId: ${{ "{{ secrets.appleId" }} }}
          appleIdPassword: ${{ "{{ secrets.appleIdPassword" }} }}
          CSC_LINK: ${{ "{{ secrets.CSC_LINK" }} }}
          CSC_KEY_PASSWORD: ${{ "{{ secrets.CSC_KEY_PASSWORD" }} }}
          AWS_ACCESS_KEY_ID: ${{ "{{ secrets.AWS_ACCESS_KEY_ID" }} }}
          AWS_SECRET_ACCESS_KEY: ${{ "{{ secrets.AWS_SECRET_ACCESS_KEY" }} }}
        run: |
          yarn --cwd workstation build
          yarn --cwd workstation deploy
```

# [Troubleshooting](#troubleshooting)

# [Summary](#summary)

### [References](#references)

- [electron-builder](https://www.electron.build/code-signing).
