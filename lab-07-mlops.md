# Lab 07 - MLOps with Azure Machine Learning, Azure Pipelines, and GitHub Actions

This lab covers MLOps using Azure DevOps and GitHub, training and deployment of models, real time scoring using a REST API endpoint.

The high-level steps covered in the lab are:

- Explore lab scenario
- Observe the execution of CI/CD pipeline all the way to the update on the REST API endpoint
  - Explore the execution and artifacts of the existing model training Azure DevOps pipeline: AML workspace with notebooks, experiments, and data
  - Explore the execution of the existing  Azure DevOps release pipeline
- Using GitHub actions to trigger CI/CD pipeline by committing a simple code change
- Keep gates within Azure Pipeline to preempt the triggers before the pipeline moves stages
- Approve changes after validation to see it move stages
- Repeat previous steps with data drift triggers

- [Lab 07 - MLOps with Azure Machine Learning, Azure Pipelines, and GitHub Actions](#lab-07---mlops-with-azure-machine-learning-azure-pipelines-and-github-actions)
  - [Lab Overview](#lab-overview)
  - [Exercise 1: Create a model training pipeline](#exercise-1-create-a-model-training-pipeline)
    - [Task 1: Connect to Azure DevOps](#task-1-connect-to-azure-devops)
    - [Task 2: Explore the pipeline's connection to GitHub](#task-2-explore-the-pipelines-connection-to-github)
    - [Task 3: Explore the Azure Machine Learning pipeline's stages](#task-3-explore-the-azure-machine-learning-pipelines-stages)
    - [Task 4: Access the pipeline and trained models from Azure Machine Learning Studio](#task-4-access-the-pipeline-and-trained-models-from-azure-machine-learning-studio)
  - [Exercise 2: Create a model deployment pipeline](#exercise-2-create-a-model-deployment-pipeline)
    - [Task 1: Explore the deployment pipeline's stages](#task-1-explore-the-deployment-pipelines-stages)
    - [Task 2: Explore the pipeline endpoint from Azure Machine Learning Studio](#task-2-explore-the-pipeline-endpoint-from-azure-machine-learning-studio)
  - [Exercise 3 - Explore manual intervention option in Azure DevOps Pipelines](#exercise-3---explore-manual-intervention-option-in-azure-devops-pipelines)
    - [Task 1: Add a manual intervention task to the pipeline](#task-1-add-a-manual-intervention-task-to-the-pipeline)
    - [Task 2: Run the pipeline](#task-2-run-the-pipeline)
  - [Exercise 4 - Explore the execution of the existing GitHub Actions workflow](#exercise-4---explore-the-execution-of-the-existing-github-actions-workflow)
    - [Task 1: Explore the train-deploy-workflow GitHub Action](#task-1-explore-the-train-deploy-workflow-github-action)
    - [Task 2: Trigger the train-deploy-workflow GitHub Action](#task-2-trigger-the-train-deploy-workflow-github-action)
    - [Task 3: Explore the artifacts the train-deploy-workflow Action created](#task-3-explore-the-artifacts-the-train-deploy-workflow-action-created)
  - [Exercise 5 - Trigger the MLOps Pipeline when the training data changes](#exercise-5---trigger-the-mlops-pipeline-when-the-training-data-changes)
    - [Task 1: Update the training data](#task-1-update-the-training-data)
    - [Task 2: Explore the triggered workflow](#task-2-explore-the-triggered-workflow)

## Lab Overview

Managing a virtually non-stop flux of incoming research documents should be based on a fully automated and traceable process. Everything from data to code must be tracked and monitored. The complex processes of Machine Learning model training and operationalization require secure, end-to-end approaches that allow teams of developers and analysts to iterate through multiple versions of the solution.

Using GitHub and GitHub Actions, we will build an end-to-end Machine Learning process, where data and code act like inputs and actionable REST API endpoints are the result. Our pipelines will automate building and operationalizing the Machine Learning model that classifies research papers.

## Exercise 1: Create a model training pipeline

### Task 1: Connect to Azure DevOps

1. Open the [Azure DevOps portal](https://dev.azure.com/) and select the **Sign in to Azure DevOps** link.

    ![Sign-in to Azure DevOps](./media/01-devops.png)

2. To sign-in, use the Azure credentials provided by the lab environment.

3. In Azure DevOps, open the pre-created project named `odluserXXXXXX-project` and navigate to the **Pipelines** section and observe the two pipelines pre-created for you: `Model-Train-Register-CI` and `Model-Deploy-CD`.

    ![Locate the CI/CD pipelines](./media/01-devops-pipelines.png)

### Task 2: Explore the pipeline's connection to GitHub

1. Select the `Model-Train-Register-CI` pipeline and then select the first run as illustrated in the picture below.

    ![Open the CI pipeline run](./media/01-devops-trainregister-ci.png)

2. On the run results page, check the **Summary** section to understand how the pipeline is linked to GitHub. Right click on the repository name on the Repository and version column as illustrated below and choose to open it in a new tab.

    ![Open the GitHub repository](./media/01-devops-opengitrepository.png)

3. You will be prompted to sign in with the Git credentials provided for you in the lab environment.

4. You will be asked to verify your account, so you should open your user's mailbox on https://outlook.office365.com/ to be able to receive the verification codes for GitHub authentication. Use the same GitHub user account credentials to open Outlook.

5. In GitHub, you will see the `azure-ml-data-science-400-lab07` repository that was pre-generated under your GitHub account.

    ![View the GitHub repository](./media/01-devops-labrepository.png)

6. If the repository is not automatically opened, select it from the available repositories list.

    ![Locate the GitHub repository](./media/01-github-selectrepository.png)

7. Spend a few moments to browse the repository structure. Focus on the `.pipelines` folder which contains the YAML scripts used by the Azure DevOps pipelines discussed later in this exercise.

    ![Browse the GitHub repository .pipelines folder](./media/01-github-pythonscripts.png)

### Task 3: Explore the Azure Machine Learning pipeline's stages

1. Going back to the Azure DevOps portal browser tab, on the `Model-Train-Register-CI` pipeline run page observe the two stages of the CI pipeline. Select the **Model CI** stage to open the execution log for this pipeline run.

    ![Expand CI pipeline stages](./media/01-expandstage.png)

2. Select the **Publish Azure Machine Learning Pipeline** task node under **Model CI Pipeline**. Scroll down on the execution log window and observe the publishing of the Azure Machine Learning pipeline.The AML pipeline involves three main steps: Train, Evaluate and Register model.

    ![Observe how the AML Pipeline was published](./media/023-publishAMLpipeline.png)

3. Going back to the pipeline stages page, expand the **Train and evaluate model stage** from the **Expand stage** button.

    ![Expand pipeline stages](./media/022-expandstage.png)

4. Also you should inspect the artifact of the training stage:

    ![Inspect artifacts of the training stage](./media/023-pipelineartifacts.png)

5. Going back to the pipeline stages page, select the **Train and evaluate model stage** to open the run execution log.

    ![Open Run execution log](./media/023-openrunexecutionlog.png)

6. Expand the **Train and evaluate model** task nodes and observe the three main tasks involved in this stage: **Get pipeline ID for execution**, **Trigger ML Training Pipeline** and **Publish artifact is new model was registered**.

    ![Observe tasks of the train and evaluate model stage](./media/024-trainandevaluatestage.png)

### Task 4: Access the pipeline and trained models from Azure Machine Learning Studio

1. To be able to understand the artifacts published in your Azure Machine Learning Workspace, open a new browser tab and sign in to the [Azure Portal](https://portal.azure.com) with the Azure credentials provided in the lab. Open the available Resource Group, locate and select the Machine Learning workspace that was pre-created in the lab environment.

    ![Inspect artifacts of the training stage](./media/024-locatethemachinelearningworkspace.png)

2. Select **Launch studio** to navigate to the **Azure Machine Learning Studio**.

3. In the [Azure Machine Learning Studio](https://ml.azure.com), select **Pipelines** from the left navigation menu, go to **Pipeline endpoints** and check the published training pipeline in the `azure-ml-data-science-400-XXXXXX` workspace.

    ![Inspect published ML pipeline](./media/025-checktrainingpipeline.png)

4. The build pipeline for training automatically triggers every time there's a change in the master branch. After the pipeline is finished, you'll see a new model in the ML Workspace. Navigate to the **Models** section in [Azure ML Studio](https://ml.azure.com/), using the left navigation menu and check the  registered model named `COVID19Articles_model.pkl`.

     ![Check the registered model](./media/026-registeredmodel.png)

## Exercise 2: Create a model deployment pipeline

The release deployment and batch scoring pipelines have the following behaviors:

- The pipeline will automatically trigger on completion of the Model-Train-Register-CI pipeline for the master branch.
- The pipeline will default to using the latest successful build of the Model-Train-Register-CI pipeline. It will deploy the model produced by that build.
- You can specify a Model-Train-Register-CI build ID when running the pipeline manually. You can find this in the url of the build, and the model registered from that build will also be tagged with the build ID. This is useful to skip model training and registration, and deploy/score a model successfully registered by a Model-Train-Register-CI build.

    > **Note:** The pipeline you will review has the following stages:
    >
    > - Deploy to ACI
    >   - Deploy the model to the QA environment in Azure Container Instances.
    >   - Smoke test: scoring sample step

### Task 1: Explore the deployment pipeline's stages

1. In Azure DevOps, open your project and navigate to the **Pipelines** section. Select the `Model-Deploy-CD` pipeline and then select the first run as illustrated in the picture below.

    ![Open the CD pipeline run](./media/01-devops-deploy-cd.png)

2. The first time when the CD pipeline runs, it will use the latest model created by the Model-Train-Register-CI pipeline. Given that the pre-created pipeline has already finished the first execution, check the displayed execution stages as illustrated below.

    ![Run the pipeline](./media/031-runstages.png)
  
3. Select the first stage: Deploy to ACI to open the execution log. Expand the list of executed tasks under the stage node. Select the **Parse Json for Model Name and Version** task. You can see how the model name and version are downloaded from the `Model-Train-Register-CI` pipeline.

    ![Extract model name and last version](./media/031-runpipeline.png)

4. Select the **Deploy to ACI (CLI)** node and scroll the execution log to see the performed steps inside the deployment task.

    ![Deploy the last version of model to ACI](./media/032-deploytoACI.png)

5. Select the **Smoke test**: The test sends a sample query to the scoring web service and verifies that it returns the expected response. Have a look at the smoke [test code](https://github.com/solliancenet/azure-ml-data-science-400-lab07-starter/blob/master/COVID19Articles/ml_service/util/smoke_test_scoring_service.py) for more details on the steps involved there.

    ![Smoke test execution](./media/033-smoketest.png)

### Task 2: Explore the pipeline endpoint from Azure Machine Learning Studio

1. To be able to understand the artifacts published in your Azure Machine Learning Workspace, go back to the Azure Machine Learning Studio page already opened in your browser and move directly to step 8. If you don't find ML Studio opened from the previous task, open a new browser tab and sign in to the [Azure Portal](https://portal.azure.com) with the Azure credentials provided in the lab. Open the available Resource Group, locate and select the Machine Learning workspace that was pre-created in the lab environment.

    ![Inspect artifacts of the training stage](./media/024-locatethemachinelearningworkspace.png)

2. Select **Launch studio** to navigate to the **Azure Machine Learning Studio**.

3. In the [Azure Machine Learning Studio](https://ml.azure.com), select **Endpoints** (1) from the left navigation menu, go to **Real-time endpoints** (2) and select the published ACI endpoint named `mlops-aci` (3).

    ![Inspect published ACI endpoint](./media/035-aciendpoint.png)

4. When opening the ACI endpoint details page, you can identify the REST endpoint used for scoring in the Smoke test example on step 5 of this Task.

    ![Locate REST endpoint url used for scorings](./media/035-RESTendpoint.png)

## Exercise 3 - Explore manual intervention option in Azure DevOps Pipelines

In Azure DevOps Pipelines, teams can also take advantage of the Approvals and Gates feature to control the workflow of the deployment pipeline. Each stage in a release pipeline can be configured with pre-deployment and post-deployment conditions that can include waiting for users to manually approve or reject deployments, as well as using automated systems to enforce specific conditions.

### Task 1: Add a manual intervention task to the pipeline

1. Open the [Azure DevOps portal](https://dev.azure.com/) and if not already signed in, select the **Sign in to Azure DevOps** link. (To sign-in, use the Azure credentials provided by the lab environment.)

2. In Azure DevOps, open the pre-created project named `odluserXXXXXX-project`, navigate to the **Pipelines** section and observe the two pipelines pre-created for you: `Model-Train-Register-CI` and `Model-Deploy-CD`. Select the **Model-Train-Register-CI** pipeline.

    ![Locate the CI/CD pipelines](./media/01-devops-pipelines.png)

3. Select **Edit** to open the pipeline designer.

    ![Edit the CI pipeline](./media/038-edit-ci-pipeline.png)

4. In the pipeline YAML definition, locate the beginning of the job: Training_Run_Report (1). In the **Tasks** section located on the right, search for the **Manual intervention** task (2) and then select it (3).

    ![Locate the manual intervention task](./media/039-create%20task.png)

5. Fill the required fields for your manual intervention task:
   - **Instructions**: `Please approve the registered model.  This will allow the deployment to continue.`
   - **Notify users**: enter the github account email address of the form `github_cloudlabsuser_XXXX@cloudlabsaiuser.com`

    ![Configure the manual intervention task](./media/039-manualinterventionconfig.png)
6. Selecting **Add** will insert the YAML definition of the task at the end of the **Trigger ML Training Pipeline** stage.

   ![Manual intervention task YAML](./media/039-manualintervention.png)

   > **Note:** Be sure that the level of indentation for the newly-created manual intervention task on line 90 is the same as the task on line 83.  YAML is a whitespace-sensitive markup language.

### Task 2: Run the pipeline

1. **Save** and **Run** the pipeline. Select the new pipeline Run from the **Runs** list and select the Train and evaluate model stage to open the execution log page.

    ![Open execution log](./media/039-openexecutionlog.png)

## Exercise 4 - Explore the execution of the existing GitHub Actions workflow

GitHub workflows are triggered based on events specified inside workflows. These events can be from inside the github repo like a push commit or can be from outside like a webhook.
We have created sample workflow file train_deploy.yml to train the model and deploy the ACI endpoint, similar to what we did in the previous exercise using DevOps Pipelines. This GitHub workflow trains the model in a first job and, on successful training completion, deploys the model inside the second job. You will activate this workflow by doing a commit to any file under the included path: `COVID19Articles_GH/` folder.

### Task 1: Explore the train-deploy-workflow GitHub Action

1. Sign-in to [GitHub](https://github.com) with your GitHub account.

2. Navigate to the already generated repository for this lab, named `azure-ml-data-science-400-lab07`. If the repository is not automatically opened, please select it from the available repositories list.

    ![Locate GitHub repository](./media/01-github-selectrepository.png).

3. Navigate to the `.github/workflows` folder and open the train_deploy.yml (1) workflow definition file . Observe the two jobs defined for the GitHub Actions workflow: **train-register** and **deploy** (3). The workflow is triggered (2) when a code change is committed inside the **COVID19Articles_GH** repository folder in the `master` branch.

   ![Inspect GitHub Actions workflow definition](./media/036%20-%20github-actions-workflow-definition.png)

4. With the repository opened, select the **Actions** section from the top navigation menu. Observe the active workflow that was already initiated at the first code commit, when the `.github/workflows/train_deploy.yml` definition file was initially created in the repository.

   ![Check GitHub Actions workflows](./media/036%20-%20github-actions-workflows.png)

### Task 2: Trigger the train-deploy-workflow GitHub Action

1. Going back to the repository files, open the **COVID19Articles_GH/train** folder and select the `train_aml.py` code file. 

    ![GitHub Actions locate train_aml.py](./media/036%20-%20githubactions-locate-trainaml.png)

2. Open the `train_aml.py` code file in edit mode.

    ![GitHub Actions edit train_aml.py](./media/036%20-%20githubactions-editfile.png)

3. Go to line 172 and change the `max_depth` argument value from **5** to **4** (1). Commit your change (2).

    ![GitHub Actions change a line of code](./media/036%20-%20githubactions-commitchange.png)

4. Navigate to the **Actions** section in GitHub and observe how your code change automatically triggered the GitHub Action named **train-deploy-workflow**. Select the workflow run.

    ![GitHub Actions Workflow started](./media/github-repo-startworkflow.png)

5. Observe the two jobs of the workflow run. Select each stage and check the job steps in the execution log.

    ![GitHub Actions Workflow jobs](./media/github-repo-wokflowstages.png)

### Task 3: Explore the artifacts the train-deploy-workflow Action created

1. While waiting for the workflow to execute, watch it how it moves from the first job to the second one and switch back to the [Azure Machine Learning Studio page](https://ml.azure.com/) to see the generated artifacts of each step: register dataset, submit experiment, register model and deploy ACI endpoint.

2. Navigate to the **Experiments** section (1). You should be able to see the new experiment **COVID19_Classification_GH** (2) that was started by the GitHub Actions Workflow you triggered. Expand the experiment to see the created experiment Run.

    ![GitHub Actions triggered ML experiment run](./media/036%20-%20github-startexperiment.png)

3. Navigate to the **Models** (1) page. You should be able to see the model registered by the GitHub Actions workflow, named **COVID19Articles_githubactions** (2).

    ![GitHub Actions triggered ML model register step](./media/036%20-%20github-registermodel.png)

4. Check the deploy job execution log while it is executing. When the deploy stage completes, in ML Studio, navigate to the **Endpoints** (1) page. You should be able to see the ACI endpoint named `mlops-aci-githubactions` (2) published inside the GH Actions `deploy` job.

    ![GitHub Actions deploy job execution](./media/github-actions-deployjob.png)

    ![GitHub Actions triggered ML endpoint](./media/036%20-%20github-ACIendpointbyGH.png)

## Exercise 5 - Trigger the MLOps Pipeline when the training data changes

In the Azure Portal, we prepared an Azure Function that triggers the above described GitHub workflow every time a new version of the COVID16Articles.csv file is uploaded in the datastore.

### Task 1: Update the training data

1. Open the [Azure portal](https://portal.azure.com/) and sign-in using the Azure credentials provided by the lab environment. 

2. From the left navigation menu, select the **Storage accounts** option and locate the storage named `azuremldatasciXXXXXX` as illustrated below. 

    ![Locate default datastore](./media/040-locatestorageaccount.png)

3. Select `azuremldatasciXXXXXX` storage account and select **Containers** from the **Data storage** menu.  Select the `azureml-blobstore-{container_id}` container. This is the storage container linked to the default **Datastore** used by Azure Machine Learning Workspace.

    ![Select AzureML Container](./media/040-select-azureml-container.png)

4. Navigate to the `training-data\COVID19Articles.csv` file. Download it to your computer and change anything inside the file.

5. Upload the new version of COVID19Articles.csv in the same `training-data` folder by using the Upload button from the top menu bar.

    ![Upload a new version of data](./media/040-upload%20the%20new%20version.png)

6. Select the option to overwrite the file since it already exists in the container.

    ![Overwrite COVID19Articles.csv file](./media/040-overwritefile.png)

### Task 2: Explore the triggered workflow

1. Open the GitHub portal, select the `azure-ml-data-science-400-lab-07` repository and select **Actions** from the top menu bar. Notice how the data file update triggered your GitHub Actions workflow execution.

    ![Blob updated triggered GitHub Actions](./media/040-githubtriggeredaction.png)
