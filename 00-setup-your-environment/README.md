# 00 - Setup your environment

__This guide is part of the [Azure Spring Cloud training](../README.md)__

Setting up all the necessary prerequisites in order to expeditiously complete the lab.

---

## Creating Azure Resources

To save time, we provide an ARM template for creating all the Azure resources you will need for this lab other than the Azure Spring Cloud instance itself. Use the Deploy to Azure button below.

> 💡 Use the following settings for deploying the Azure Template:
>
> * Create a new resource group.
> * In the location field, select the nearest region from [the list of regions where Azure Spring Cloud is available](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all).
> * Save the MySQL password you specify in this step. You will need it in section 6. If you don't set one, it will be `super$ecr3t`.

[![Deploy to Azure](media/deploybutton.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fazure-spring-cloud-training%2Fmaster%2F00-setup-your-environment%2Fazuredeploy.json?WT.mc_id=azurespringcloud-github-judubois)

>⏱ The resource provisioning will take some time. __Do not wait!__ Continue with the workshop.

## Prerequisites

This training lab requires the following to be installed on your machine:

* [JDK 1.8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)
* A text editor or an IDE. If you do not already have an IDE for Java development, we recommend using [Visual Studio Code](https://code.visualstudio.com/?WT.mc_id=azurespringcloud-github-judubois) with the [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack&WT.mc_id=azurespringcloud-github-judubois).
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest&WT.mc_id=azurespringcloud-github-judubois) version 2.0.80 or later. You can check the version of your current Azure CLI installation by running:

  ```bash
  az --version
  ```

* The Bash shell. While Azure CLI should behave identically on all environments, some shell semantics may need to be modified for use with other shells. To complete this training on Windows, use [Git Bash that accompanies the Windows distribution of Git](https://git-scm.com/download/win).


* The `jq` utility. On Windows, download [this Windows port of JQ](https://github.com/stedolan/jq/releases) and add the following to the `~/.bashrc` file (create the file from scratch if it does not exist):


   ```bash
   alias jq=<JQ Download location>/jq-win64.exe
   ```

* 🚧 The `spring-cloud` extension for Azure CLI. You can install this extension after installing Azure CLI by running `az extension add -n spring-cloud -y`.

> 💡 In sections 9 and 10, you will access the UI of the Microservice applications in a web browser. Use the [new Edge](https://microsoft.com/edge/?WT.mc_id=azurespringcloud-github-judubois), Google Chrome, or Firefox for these sections.

The environment variable `JAVA_HOME` should be set to the path of the JDK installation. The directory specified by this path should have `bin`, `jre`, and `lib` among its subdirectories. Further, ensure your `PATH` variable contains the directory `${JAVA_HOME}/bin`. To test, type `which javac` into bash shell ensure the resulting path points to a file inside `${JAVA_HOME}/bin`.

You can then use Visual Studio Code or an IDE of your choice.


➡️ Next guide: [01 - Create an Azure Spring Cloud instance](../01-create-an-azure-spring-cloud-instance/README.md)
