---
layout:       post
title:        "Getting Started with BigQuery"
author:       "Eric"
header-style: text
catalog:      true
tags:
    - BigQuery
    - Google
---

> This is about how to learn Kubeflow in ML Task 

# Building Your First Kubeflow Pipeline: A Comprehensive Guide


Kubeflow is an open-source platform designed to be end-to-end, facilitating each step of the Machine Learning (ML) workflow. It aims to make deployments of ML workflows on Kubernetes simple, portable, and scalable. One of its most powerful features is Kubeflow Pipelines, a platform for building, deploying, and managing ML workflows based on Docker containers.

Why should you care? The power of Kubeflow Pipelines lies in their ability to automate and streamline the entire machine learning process, from data ingestion to model deployment. This not only saves time but also helps to maintain consistency and quality in your projects.

In this post, we'll explore how to build your first Kubeflow Pipeline from scratch. By the end, you'll have a solid understanding of what Kubeflow is and how you can use it to construct an ML workflow.

[![image/png](https://cdn-uploads.huggingface.co/production/uploads/6262fb8e6f289e10ee04f83b/vUc6IgSbrxn5T9jaR5_Ov.png)](https://cdn-uploads.huggingface.co/production/uploads/6262fb8e6f289e10ee04f83b/vUc6IgSbrxn5T9jaR5_Ov.png)

[](#kubeflow-and-machine-learning-workflows)Kubeflow and Machine Learning Workflows
-----------------------------------------------------------------------------------

Kubeflow is a platform for data scientists and machine learning engineers containing the best of both worlds' functionalities. Data scientists can use Kubeflow to experiment with ML models and orchestrate their experiments on Kubernetes in the most efficient way. Machine learning engineers can use Kubeflow to deploy ML systems to various environments for development, testing, and production serving. The diagram below exemplifies two distinct phases in a machine learning project: (i) the Experimental Phase and (ii) the Production Phase.

![drawing](https://cdn-uploads.huggingface.co/production/uploads/6262fb8e6f289e10ee04f83b/EjI6ScdGpiPZ9AXAhxMwj.png)

Kubeflow components in the ML workflow

Kubeflow has a lot of different components to support nearly all the steps in the pipeline. For example, for tuning the hyperparameters of the model, Kubeflow has a component called "Katib".

Kubeflow also aligns well with MLOps principles, which aim to bridge the gap between machine learning and operations. By offering a unified workflow, Kubeflow makes it easier to manage ML projects from experimentation to production, incorporating aspects of DevOps and facilitating collaboration between data scientists and operations personnel.

[](#three-principles-of-kubeflow)Three Principles of Kubeflow
-------------------------------------------------------------

*   **Composability**: Kubeflow is highly composable, allowing you to use different versions of TensorFlow or any other ML libraries for different parts of your machine learning pipeline if needed.
    
*   **Portability**: With Kubeflow, you can run your entire machine learning project anywhere you are running Kubernetes. It abstracts away platform-specific details, enabling you to write your code once and run it whether you're on your laptop or a cloud-based cluster.
    
*   **Scalability**: Kubeflow is designed to scale, providing you with the flexibility to allocate more resources when they're needed and release them when they're not. Whether you're using CPUs, GPUs, or TPUs, Kubeflow helps you make the most of your hardware resources.
    

![drawing](https://cdn-uploads.huggingface.co/production/uploads/6262fb8e6f289e10ee04f83b/qUHRMRwla4-QHNJfJyZ2m.png)

Kubeflow Conceptual Entities

[](#installing-kubeflow)Installing Kubeflow
-------------------------------------------

There are two ways to get started with Kubeflow:

1.  Install it using a packaged distribution, which is simple and straightforward. You can find more information on installing Kubeflow with packaged distributions [here](https://www.kubeflow.org/docs/started/installing-kubeflow/#packaged-distributions).
2.  Install it using manifests, which is more advanced. Detailed instructions can be found [here](https://www.kubeflow.org/docs/started/installing-kubeflow/#manifests).

Packaged distributions are developed and supported by the respective maintainers. For example, Microsoft maintains Kubeflow on Azure. You can find a complete list of distributions in the table below:

![drawing](https://cdn-uploads.huggingface.co/production/uploads/6262fb8e6f289e10ee04f83b/1-phtZJFcb8Bqk7EIda6P.png)

Kubeflow Packaged Distributions

You can also refer to the blog titled [Kubeflow: How to Install and Launch Kubeflow on your Local Machine](https://towardsdatascience.com/kubeflow-how-to-install-and-launch-kubeflow-on-your-local-machine-e0d7b4f7508f) for more detailed installation instructions.

After installing, you will have access to the Kubeflow Dashboard, which resembles the figure below.

![drawing](https://cdn-uploads.huggingface.co/production/uploads/6262fb8e6f289e10ee04f83b/y5theIUw8vHFxQLI7IAZz.png)

Kubeflow Dashboard UI View

[](#a-simple-python-script-for-demonstration)A Simple Python Script for Demonstration
-------------------------------------------------------------------------------------

Before we get into Kubeflow Pipelines, let's create a simple Python script to understand what we're aiming to convert into a pipeline. This script will simulate a very basic ML workflow where we read some data, perform a trivial computation, and save the result.

Here is how you can do it:

```
# my_script.py

def read_data():
    data = [1, 2, 3, 4, 5]
    return data

def compute_average(data):
    return sum(data) / len(data)

def save_result(value, filename='result.txt'):
    with open(filename, 'w') as f:
        f.write(str(value))

if __name__ == "__main__":
    data = read_data()
    avg = compute_average(data)
    save_result(avg)

```


This Python script has three functions: one for reading data (`read_data`), one for computing the average (`compute_average`), and one for saving the result (`save_result`). Our goal will be to convert each of these functions into a Kubeflow pipeline component.

In the next section, we'll delve into Kubeflow Components and show you how to build one from this simple Python script.

[](#understanding-kubeflow-components)Understanding Kubeflow Components
-----------------------------------------------------------------------

Kubeflow Components are the building blocks of a pipeline. Essentially, each component is a self-contained piece of code that performs one step in your ML workflow. It runs independently and does one thing well, like read data, transform features, train a model, or serve an endpoint.

Let's convert our simple Python script into a Kubeflow Component. We'll be using the Kubeflow Pipelines SDK's compiler module to do this.

### [](#creating-a-kubeflow-component)Creating a Kubeflow Component

First, let's turn each function in our Python script into a separate component.

```
from kfp import components

def read_data() -> list:
    data = [1, 2, 3, 4, 5]
    return data

def compute_average(data: list) -> float:
    return sum(data) / len(data)

def save_result(value: float, filename: str = 'result.txt'):
    with open(filename, 'w') as f:
        f.write(str(value))

# Compile the component
read_data_op = components.func_to_container_op(func=read_data,
                                output_component_file='read_data_component.yaml',
                                base_image='python:3.7',  # You can specify the base image here
                                packages_to_install=[])  # Any packages that need to be installed can be added here

compute_average_op = components.func_to_container_op(func=compute_average,
                                output_component_file='compute_average_component.yaml',
                                base_image='python:3.7',
                                packages_to_install=[])

components.func_to_container_op(save_result,
                                output_component_file='save_result_component.yaml')

```


Components are the building blocks of a Kubeflow Pipeline. In our example, we used the `func_to_container_op` function to convert a Python function into a component. While doing so, you may have noticed two parameters, `base_image` and `packages_to_install`.

The `base_image` parameter specifies the Docker image that will be used as the execution environment for the component. In simpler terms, it's like the operating system of the component. This image should have all the necessary software to run your code.

*   **Why is it Important?**: Different codebases may require different runtime environments. For example, if you are working on a TensorFlow project, you may choose a base image that has TensorFlow pre-installed.
    
*   **Example Usage**:
    
    ```
base_image='tensorflow/tensorflow:2.4.0'

```

    
*   **Default**: If you don't specify a `base_image`, the default Python image is used, which is a minimal image with Python installed.
    

The `packages_to_install` parameter allows you to install additional Python packages needed for your code to run. This is an array of package names that will be installed using pip.

*   **Why is it Important?**: Sometimes your code depends on third-party libraries that are not present in the base image. In such cases, you can provide the names of these packages, and they will be installed when the component runs.
    
*   **Example Usage**:
    
    ```
packages_to_install=['numpy', 'pandas']

```

    
*   **Note**: Be careful when specifying packages, as installing incompatible versions can break your component.
    

[](#creating-the-kubeflow-pipeline)Creating the Kubeflow Pipeline
-----------------------------------------------------------------

Having defined our components, the next step is to arrange them into a pipeline. To do this, you'll use Kubeflow's Domain Specific Language (DSL) to link components together. Once you have your components compiled and saved as `.yaml` files, you're ready to assemble them into a pipeline. For this, we'll write a Python function that uses the Kubeflow Pipelines SDK to define the pipeline's structure.

In Kubeflow Pipelines, a pipeline is essentially a Python function decorated with @dsl.pipeline. Within this function, you can use the components as building blocks. Here's how you can define a Kubeflow Pipeline using our compiled components.

### [](#importing-compiled-components)Importing Compiled Components

You can import your compiled components like this:

```
import kfp
from kfp import dsl

read_data_op = kfp.components.load_component_from_file('read_data_component.yaml')
compute_average_op = kfp.components.load_component_from_file('compute_average_component.yaml')
save_result_op = kfp.components.load_component_from_file('save_result_component.yaml')

```


### [](#assembling-the-pipeline)Assembling the Pipeline

After loading the components, let's stitch them together to form a pipeline.

```
@dsl.pipeline(
    name='My first pipeline',
    description='A simple pipeline that computes the average of an array.'
)
def my_pipeline():
    read_data_task = read_data_op()
    compute_average_task = compute_average_op(read_data_task.output)
    save_result_task = save_result_op(compute_average_task.output)

# Compile the pipeline
kfp.compiler.Compiler().compile(my_pipeline, 'my_pipeline.yaml')

```


This pipeline first reads data using `read_data_op`, then computes the average using `compute_average_op`, and finally saves the result using `save_result_op`.

Additionally, here's a snippet that shows how to pass parameters between components:

```
@dsl.pipeline(
    name='My parameterized pipeline',
    description='A simple pipeline that reads data and takes a parameter.'
)
def my_pipeline(my_param: int):
    read_data_task = read_data_op()
    another_task = another_component_op(my_param)

```


This allows you to create more dynamic and configurable pipelines.

In the following sections, we'll look into how to deploy this pipeline and best practices to follow while working with Kubeflow Pipelines.

[](#deploying-the-kubeflow-pipeline)Deploying the Kubeflow Pipeline
-------------------------------------------------------------------

After constructing and compiling your pipeline, the next step is to deploy it onto the Kubeflow platform. This involves uploading the compiled `.yaml` file and then running the pipeline.

### [](#uploading-the-pipeline)Uploading the Pipeline

1.  Navigate to your Kubeflow dashboard.
2.  Go to the `Pipelines` section.
3.  Click on `Upload Pipeline`.
4.  Browse and select the `my_pipeline.yaml` file.

Once uploaded, you'll see your pipeline listed among any others you may have uploaded.

### [](#running-the-pipeline)Running the Pipeline

1.  Click on the pipeline you've just uploaded.
2.  Hit the `Run` button.
3.  Give your run a name and click `Start`.

Now you can monitor the pipeline as it progresses through each stage. Successful execution will indicate that your pipeline has been deployed correctly.

![drawing](https://cdn-uploads.huggingface.co/production/uploads/6262fb8e6f289e10ee04f83b/6MXuiPZ13SRHsy45w-16a.png)

Example Kubeflow Pipeline View

[](#best-practices)Best Practices
---------------------------------

When you are working with Kubeflow Pipelines, certain best practices can help you make the most out of the platform. Below are some guidelines to consider for a smoother experience:

### [](#version-control-components)Version Control Components

*   Ensure that every version of your component is well-documented and version-controlled. This will make it easier to debug and update your pipelines in the future.

### [](#error-handling)Error Handling

*   Make sure to include error-handling mechanisms in your components. This can be done by catching exceptions in the Python code and logging meaningful messages.

```
# Example of a Python function with error handling
def read_data() -> int:
    try:
        # code to read data
        data = [1, 2, 3, 4, 5]
        return data
    except Exception as e:
        print(f"An error occurred: {e}")
        return -1

```


### [](#dependency-management)Dependency Management

*   Specify all dependencies explicitly, either in your Dockerfile or as part of your component's metadata.

### [](#component-reusability)Component Reusability

*   Design components to be reusable so that you can plug them into different pipelines as needed.

### [](#monitor-resources)Monitor Resources

*   Keep an eye on the resources (CPU, memory, etc.) that your pipeline uses. Optimize components to be as efficient as possible.

In summary, Kubeflow Pipelines offer a streamlined way to take your ML project from a simple script to a robust, end-to-end workflow. We've covered everything from setting up your environment to building and deploying your first Kubeflow Pipeline.

[](#extending-the-basics-to-real-world-ml-projects)Extending the Basics to Real-World ML Projects
-------------------------------------------------------------------------------------------------

So far, our examples have been extremely basic for the sake of clarity. However, don't underestimate the power of Kubeflow Pipelines; the principles we've covered scale impressively to real-world machine learning projects.

#### [](#real-world-use-cases)Real-World Use Cases

Each component you define can represent a step in your typical machine learning workflow. Here's how you can map Kubeflow components to your machine learning project:

1.  **Data Collection**: The `read_data` component can be expanded to collect data from various sources like databases, Excel files, or APIs.
    
2.  **Preprocessing**: You can have another component for data cleaning and preprocessing, transforming the raw data into a format that can be fed into ML models.
    
3.  **Data Splitting**: A component could be used for splitting the dataset into training, validation, and test sets.
    
4.  **Model Training**: Here, you can utilize a component to train a model using the preprocessed training set.
    
5.  **Evaluation**: Lastly, a component can evaluate the model using various metrics to understand how well it performs.
    

#### [](#example)Example

Let's say you have Python functions for each of these steps:

*   `read_data()`
*   `preprocess_data()`
*   `train_test_split()`
*   `train_model()`
*   `evaluate_model()`

Each of these functions can be converted to a Kubeflow component using `func_to_container_op`. Once they are components, you can arrange them in a pipeline just like we did with our simple `read_data` and `compute_average` components. This enables you to automate the entire machine learning workflow!

* * *

And that's a wrap! Hopefully, you now have a solid foundation to start building your own Kubeflow Pipelines, whether it's for simple tasks or complex machine learning workflows. Remember, the sky's the limit when it comes to what you can achieve with Kubeflow!

We've covered a lot of ground in this post—from setting up your environment to building and deploying your very first Kubeflow Pipeline. By now, you should have a solid understanding of what Kubeflow is, what Kubeflow Pipelines are, and how they fit into the bigger picture of Machine Learning workflows.

Kubeflow Pipelines are an essential tool for automating and scaling your ML workflows. As you dive deeper into ML projects, the ability to create robust, scalable pipelines will become increasingly valuable.

For more information and further study, feel free to visit the [Kubeflow official documentation](https://www.kubeflow.org/docs/about/kubeflow/).

#### [](#references)References

For further reading and exploration, you might find the following resources useful:

*   [Kubeflow Official Documentation](https://www.kubeflow.org/docs/)
*   [Kubeflow Pipelines GitHub Repository](https://github.com/kubeflow/pipelines)
*   [DataCamp Blog](https://www.datacamp.com/tutorial/kubeflow-tutorial-building-and-deploying-machine-learning-pipelines)

