# Hands-on lab: Implementing and monitoring a Batch Scoring solution

**Contents**

<!-- TOC -->

- [Lab Overview](#lab-overview)
- [Prerequisites](#prerequisites)
- [Exercise 1: Implementing and monitoring a Batch Scoring solution](#exercise-1-implementing-and-monitoring-a-batch-scoring-solution)
   - [Task 1: Open Lab Notebook](#task-1-open-lab-notebook)
   - [Task 2: Execute Notebook](#task-2-execute-notebook)

## Lab overview

Azure Machine Learning Batch scoring targets large inference jobs that are not time-sensitive. It is optimized for high-throughput, fire-and-forget inference over large collections of data.

In this lab, we will be using [Azure Machine Learning Pipelines](https://docs.microsoft.com/en-us/azure/machine-learning/concept-ml-pipelines) to  run batch scoring task on a large data set, using a trained model. We will use the `NYC taxi fare predictor` model from Lab 01 to make batch predictions of taxi fares for customer based on a set of input features.  The AML pipeline would entail two steps:

1. **Preprocess data step**: In this step we will simulate generation of a large dataset, approximately 250k rows of data. The data generated in this step is passed as an input to the next step.
1. **Inference step**: In this step we will use a special type of step called `ParallelRunStep` to do batch scoring on the input data. The `ParallelRunStep` uses a configuration called `ParallelRunConfig` that allows you to control how to break up the scoring job in batches across the available compute nodes and their respective processors. 

Finally, we will also review how to monitor the batch scoring pipeline runs from within Azure Machine Learning Studio.

## Prerequisites

- Knowledge of Python
- Knowledge of training models using the Azure Machine Learning SDK

## Exercise 1: Implementing and monitoring a Batch Scoring solution

### Task 1: Open Lab Notebook

1. On the Machine Learning blade in the [Azure portal](https://portal.azure.com/), open Azure Machine Learning studio by selecting **Launch studio** from the center section of the screen.

   ![The Launch studio button is highlighted on the Machine Learning blade.](media/machine-learning-launch-studio.png "Launch Azure Machine Learning studio")

1. From within Azure Machine Learning studio navigate to **Compute, Compute instances**, and then select **Jupyter** link to open Jupyter Notebooks interface for the compute instance **ml-bootcamp-SUFFIX**.

   ![The Jupyter link is highlighted next to the ml-bootcamp-SUFFIX compute instance.](media/ml-workspace-compute-instances.png "Compute instances")

1. Check **Yes, I understand** and select **Continue** in the trusted code dialog.

   ![In the Always use trusted code dialog, Yes, I understand is checked, and the continue button is highlighted.](media/trusted-code-dialog.png "Always use trusted code")

1. From within Jupyter Notebooks interface navigate to `users/odl_user_xxxxxx/day-02/notebooks`.

1. Open `Lab03 - Implementing and monitoring a batch scoring solution`. This is the Python notebook you will step through executing in this lab.

### Task 2: Execute Notebook

1. Follow the instructions within the notebook, `Lab03 - Implementing and monitoring a batch scoring solution`, to complete the lab.
