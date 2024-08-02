# CACAO Terraform templates on the CLI

## Overview

This tutorial will guide you through the process importing a basic terraform template into CACAO.

A more detailed version of this tutorial can be found here [https://docs.jetstream-cloud.org/ui/cacao/import_terraform_template/](https://docs.jetstream-cloud.org/ui/cacao/import_terraform_template/){target=_blank}

By the end of this tutorial, you will have imported a terraform template into CACAO and deployed it using the UI to confirm that it works.

## Prerequisites

* Basic understanding of Terraform, git, and the command line.
* An OpenStack environment with access to the API (if you need to validate any terraform changes).
* Terraform installed on your local machine or VM server (if you need to validate any terraform changes).
* An SSH key pair to access the VM server running Terraform (if you need to validate any terraform changes).

## CyVerse CACAO Browser UI

One of CyVerse's test deployments for CACAO will be used for this exercise so that we can import a template using the newer metadata schemas, which will be deployed to production in the very near future.

The url for the CACAO site that we will use today is [https://cacao.cyverse.ai](https://cacao.cyverse.ai){target=_blank}

Please login to verify that your ACCESS credentials work with the CACAO test site.
!!!+ warn
    For the workshop, use the "ACCESS CI (XSEDE)" identity provider when you login.
    ![](../assets/cacao/access-ci-select-identity-provider2.png)

1. 
## Installation of the CyVerse CACAO CLI

!!! Info
    If you are using a VM provided by the workshop, the CACAO CLI is already installed. You can skip this installation section.

The CyVerse CACAO CLI is a command line tool interact with the CyVerse CACAO API. The first step is the install the cli on your local machine or to a vm.

- [Linux download for CACAO CLI](https://gitlab.com/cyverse/cacao/-/package_files/141329166/download)
- [Windows download for CACAO CLI](https://gitlab.com/cyverse/cacao/-/package_files/141329227/download)
- [MacOS (Intel) download for CACAO CLI](https://gitlab.com/cyverse/cacao/-/package_files/141329262/download)
- [MacOS (ARM) download for CACAO CLI](https://gitlab.com/cyverse/cacao/-/package_files/141329290/download)

If you're using a VM provided by the workshop or a Linux system, you can use these instructions to install the CACAO CLI.

1. If necessary, obtain a shell or terminal on the Linux system you wish to install the CACAO CLI.
2. Copy link for "Linux download for CACAO CLI" from above.
3. `curl https://gitlab.com/cyverse/cacao/-/package_files/141329166/download --output cacao.zip`
4. `unzip cacao.zip # this will create a file cacao_linux_amd64`
5. `sudo mv cacao_linux_amd64 /usr/local/bin/cacao # optional; otherwise, add it into your path`
6. `chmod +x /usr/local/bin/cacao`

## Reviewing the Terraform template we will use to import

We will be using [https://github.com/cyverse/cacao-terraform-hello-world](https://github.com/cyverse/cacao-terraform-hello-world){target=_blank} for this tutorial.

You will find a directory called `.cacao` at the root of the template and two files:

- `metadata.json`, which contains the CACAO-specific metadata for the template
- `ui.json`, which contains the UI-specific hints for the template

The `ui.json` file is optional, and if not present, then the CACAO UI will present a default naive rendering of the template.

Details about the metadata and ui schemas can be found here: [https://docs.jetstream-cloud.org/ui/cacao/import_terraform_template/](https://docs.jetstream-cloud.org/ui/cacao/import_terraform_template/){target=_blank}

!!! warn
    The UI metadata specification is currently in flux and will likely change in the near future, especially as we get feedback from the community.

### Stop! If you want to fork the template, let's do it now.

!!!+ info
    This is an optional step. You do not need to fork a template for this part of the tutorial, but you may if you wish.

## Using the CACAO CLI

### Login to CACAO CLI
1. `cacao login`
??? trip "What to do if you encounter a login issue"
    Sometimes login using the command line will fail -- a typo happens, a copy-n-paste of a token happens, using the wrong api url happens, etc -- and you need to reset your login. To reset your login, you can use the following command:

    `cacao logout`
???+ success "Expected Response"
    ```bash linenums="1" hl_lines="4"
    Please provide address of Cacao API.
    Format Should be: http://<hostname>:<port>	  or    <hostname>:<port> 
    (Developers: this should match the value of API_DOMAIN in install/config.yaml followed by "/api", e.g. http://ca.cyverse.local/api)
    Cacao API address (https://cacao.jetstream-cloud.org/api): 
    ```
2. Enter the CACAO API url: `https://cacao.cyverse.ai/api`
???+ success "Expected Response"
    ```bash linenums="4" hl_lines="2"
    Cacao API address (https://cacao.jetstream-cloud.org/api): https://cacao.cyverse.ai/api
    You will login with a CACAO API token. You need to create the token beforehand
    to use this option (e.g. create it in the credential page in Web UI).
    Enter CACAO API token:
    ```
3. In your browser, go to the [credential page](https://cacao.cyverse.ai/credentials), or click on the ":material-key: Credentials" button on the lefthand menu bar.

4. Create an API Token credential by click on the `Add Credential` button and select `API Token`. Name your API token and click `CREATE TOKEN`.

![](../assets/cacao/cacao-add-api-token.png)

5. Copy the created API Token.

![](../assets/cacao/cacao-api-token-created.png)

6. Paste the API Token into terminal from Step **2** and hit enter.
???+ success "Expected Response"
    ```bash linenums="4" hl_lines="2"
    Cacao API address (https://cacao.jetstream-cloud.org/api): https://cacao.cyverse.ai/api
    You will login with a CACAO API token. You need to create the token beforehand
    to use this option (e.g. create it in the credential page in Web UI).
    Enter CACAO API token: cptoken_aaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
    ```

7. To test a successful login, you can execute a cacao command, such as: `cacao user get --self`

### Importing a Terraform template into CACAO

`cacao template create git <source url>  <template name> --branch <git branch> --path <path to template>`

Here are the values to enter for each:

- `<source url>`: `https://github.com/cyverse/cacao-terraform-hello-world.git`
- `<template name>`: "<your student name>-hello-world" (e.g. student0001-hello-world)
- `<git branch>`: `main`
- `<path to template>`: `.`

!!!+ warn
    If you forked the template, you will need to use your forked url.

Putting this all together, your command will look similar to this: 

```bash
cacao template create git https://github.com/cyverse/cacao-terraform-hello-world.git  student0001-hello-world --branch main --path .
```

???+ success "Expected Response"
    ```bash linenums="1"
    {
        "tid": "template-cjfepm8795ktgsth2750",
        "timestamp": "2023-08-18T04:00:28.179794071Z"
    }
    ```

!!!+ note
    Save the `tid` value for later use.

### Launching a template using the CACAO UI

1. Open the CACAO UI in your browser: [https://cacao.cyverse.ai](https://cacao.cyverse.ai){target=_blank}
2. Click on the "Deployments" tab on the left-hand side.
3. Click on the "+ Add Deployment" button.
4. Select the "edwin's hello world example"/"student0001-hello-world" template. (Note: the subname of the template will be different for you.)
5. Click the "Next" button.
6. Fill out the Parameters
    1. `Choose Region`: leave as default
    2. `Instance name`: enter your student name (e.g. student0001) + date e.g. `student0001-2021-08-18`
    3. `Image`: select "Featured-Ubuntu22"
    4. `Size`: leave as default
![](../assets/cacao/access-ci-select-identity-provider2.png)
7. Click the "Next" button.
8. After reviewing the summary, click the "Submit" button.
9. You will be listed on the "Deployments" page. You can click on the deployment to see the status of the deployment.

While you are waiting, questions or we can begin the next section, "What if you want to customize a template?"

### Cleaning up (if you didn't fork your template)
!!!+ warn
    You will not be able to delete a template if there are any active deployments using it.
1. Delete the deployment in the UI or cli (`cacao deployment delete <deployment id`).
2. `cacao template delete <template id>`
???+ success "Expected Response"
    ```bash linenums="1"
    {
        "id": "template-cjfepmuf95ktgsth2750",
        "tid": "template-cjfepmuf95ktgsth2750",
        "timestamp": "2023-08-18T04:36:07.420852435Z"
    }
    ```


### What if you make changes to a template and want to use it for new deployments?

Answer: You do not need to do anything. The CACAO UI will automatically detect changes to the template on the configure branch and will use the latest branch code new deployments.

### What if you make metadata changes  to a template (e.g. `.cacao/metadata.json` or `.cacao/ui.json`) and want to use it for new deployments?

Answer: `cacao template sync <tid>`

### For those of you who forked the repo, we can experiment with template modifications
Here are a couple suggestions
* change the name of the template
* remove image (and/or size) from the template and set it

## What if you want to customize a template you don't own but is public?

If there is time, we can go through this section. Otherwise, you can try this on your own.

1. Fork the template into your own github or gitlab account
2. Add a `.cacao/metadata.json` and `.cacao/ui.json` to your forked template
3. Import the template using the `cacao template create git` command.



