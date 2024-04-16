![Diagram](imagens/portada.jpg)

# Building a Multimodal Search Engine for Text and Image with Amazon Titan Embeddings, Amazon Bedrock and LangChain.

This repo demonstrates how to build a multimodal search engine using [Amazon Titan Embeddings](https://docs.aws.amazon.com/bedrock/latest/userguide/titan-embedding-models.html), Amazon Bedrock, and [LangChain](https://python.langchain.com/docs/get_started/introduction). It covers generating text and image embeddings with Titan's state-of-the-art models, enabling semantic search and retrieval. Through Jupyter notebooks, it guides you through loading text from PDFs, generating text embeddings, splitting text into meaningful chunks with LangChain, and storing these embeddings in a [FAISS](https://python.langchain.com/docs/integrations/vectorstores/faiss/) vector database for efficient search and retrieval operations.

The repo also covers building an image search application using Amazon Titan Multimodal Embeddings and LangChain, including how to generate embeddings for images, create a vector database specifically for image data, and query the database to retrieve relevant images based on their visual content.

In a second part you'll build a Serveless Embedding APP leverage [AWS Cloud Development Kit (CDK)](https://aws.amazon.com/cdk/) to create four [AWS Lambda Functions](https://aws.amazon.com/lambda/): two responsible for embedding text and image files, and two for retrieving documents based on text or image queries. These Lambda functions will be designed to be invoked through events invocations, providing a scalable and serverless solution for my multimodal search engine.

By the end of this post, I'll have a solid understanding of how to:

- Load text from PDF files
- Generate embeddings for text and images using Amazon Titan Embeddings
- Split text into semantically meaningful chunks using LangChain
- Create local vector databases for text and image data using FAISS
- Build an image search application leveraging Titan Multimodal Embeddings
- Query the vector databases for relevant text documents and image file 
- Deploy Lambda Functions for embedding and retrieval using AWS CDK

Get ready to unlock the power of multi-modal search and unlock new possibilities in my apps!

## Jupyter notebooks
![Diagram](imagens/part_1.jpg)

**Requirements**: 
- Install boto3 - This is the [AWS SDK for Python ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingTheBotoAPI.html)that allows interacting with AWS services. Install with `pip install boto3`.
- [Configure AWS credentials](https://docs.aws.amazon.com/braket/latest/developerguide/braket-using-boto3.html) - Boto3 needs credentials to make API calls to AWS.
- Install [Langchain](https://python.langchain.com/docs/get_started/introduction), a framework for developing applications powered by large language models (LLMs). Install with `pip install langchain`.

💰 **Cost to complete**: 
- [Amazon Bedrock Pricing](https://aws.amazon.com/bedrock/pricing/)

### [Build a PDF Vector Data Base](notebooks/build_pdf_vector_db.ipynb):

Jupyter notebook for loading documents from PDFs, extracting and splitting text into semantically meaningful chunks using [LangChain](https://python.langchain.com/docs/get_started/introduction), generating text embeddings from those chunks utilizing an , generating embeddings from the text using an  [Amazon Titan Embeddings G1 - Text models](https://docs.aws.amazon.com/bedrock/latest/userguide/titan-embedding-models.html), and storing the embeddings in a [FAISS](https://python.langchain.com/docs/integrations/vectorstores/faiss/) vector database for retrieval.

### [Build a Image Vector Data Base](notebooks/build_images_vector_db.ipynb):

In this notebook, you will build an image search application with [Titan Multimodal Embeddings](https://docs.aws.amazon.com/bedrock/latest/userguide/titan-multiemb-models.html) and [LangChain](https://python.langchain.com/docs/get_started/introduction), and then storing those embeddings in [FAISS](https://python.langchain.com/docs/integrations/vectorstores/faiss/) vector database for later search.

## Serveless Embedding APP

💰 **Cost to complete**: 
- [Amazon Bedrock Pricing](https://aws.amazon.com/bedrock/pricing/)
- [Amazon Lambda Pricing](https://aws.amazon.com/lambda/pricing/)

In the second part, you'll construct a Serverless Embedding App utilizing the AWS Cloud Development Kit (CDK) to create four Lambda Functions. 

### AWS Lambda Funtions to Generating Embeddings for Text and Image Files:

![Diagram](imagens/part_2_a.jpg)

To handle the embedding process, there is a dedicated Lambda Function for each file type:

- [To generate embeddings for the text content of PDF files](serveless-embeddings/lambdas/code/build_pdf_vector_db/lambda_function.py). 

Event to trigger: 

```json
{
    "location": "REPLACE-YOU-KEY",
    "vectorStoreLocation": "REPALCE-NAME.vdb",
    "bucketName": "REPLACE-YOU-BUCKET",
    "vectorStoreType": "faiss",
    "splitStrategy": "semantic",
    "fileType": "application/pdf", 
    "embeddingModel": "amazon.titan-embed-text-v1"
  }
```
| Event  |Executing function: succeeded |
|---|---|
|![Diagram](imagens/event_1_pdf.jpg)|![Diagram](imagens/result_1_pdf.jpg)|
|||

- [To generate embeddings for images](serveless-embeddings/lambdas/code/build_image_vector_db/lambda_function.py).

Event to trigger: 

```json
{
    "location": "REPLACE-YOU-KEY-FOLDER",
    "vectorStoreLocation": "REPLACE-NAME.vdb",
    "bucketName": "REPLACE-YOU-BUCKET",
    "vectorStoreType": "faiss",
    "splitStrategy": "semantic",
    "embeddingModel": "amazon.titan-embed-image-v1"
  }
```

| Event  |Executing function: succeeded |
|---|---|
|![Diagram](imagens/event_1_image.jpg)|![Diagram](imagens/result_1_image.jpg)|
|||


>Learn [how test Lambda Functions in the console](https://docs.aws.amazon.com/lambda/latest/dg/testing-functions.html) with test events. 

### AWS Lambda Funtions to Query for Text and Image Files in a Vector DB:

![Diagram](imagens/part_2_b.jpg)

To handle the embedding process, there is a dedicated Lambda Function for each file type:

- [To retrieval text content from a vector DB](serveless-embeddings/lambdas/code/pdf_retriever_lambda/lambda_function.py)

Event to trigger: 

```json
{
  "vectorStoreLocation": "REPLACE-NAME.vdb",
  "bucketName": "REPLACE-YOU-BUCKET",
  "vectorStoreType": "faiss",
  "query": "YOU-QUERY",
  "numDocs": 5,
  "embeddingModel": "amazon.titan-embed-text-v1"
}

```
| Event  |Executing function: succeeded |
|---|---|
|![Diagram](imagens/event_2_pdf.jpg)|![Diagram](imagens/result_2_pdf.jpg)|
|||

- [To retrieval image location from a vector DB](serveless-embeddings/lambdas/code/pdf_retriever_lambda/lambda_function.py)

You can search by text or by image

- Text event to trigger
```json
{
  "vectorStoreLocation": "REPLACE-NAME.vdb",
  "bucketName": "REPLACE-YOU-BUCKET",
  "vectorStoreType": "faiss",
  "InputType": "text",
  "query":"TEXT-QUERY",
  "embeddingModel": "amazon.titan-embed-text-v1"
}
```

| Event  |Executing function: succeeded |
|---|---|
|![Diagram](imagens/event_2_image.jpg)|![Diagram](imagens/result_2_image.jpg)|
|||

- Image event to trigger
```json
{
  "vectorStoreLocation": "REPLACE-NAME.vdb",
  "bucketName": "REPLACE-YOU-BUCKET",
  "vectorStoreType": "faiss",
  "InputType": "image",
  "query":"IMAGE-BUCKET-LOCATION-QUERY",
  "embeddingModel": "amazon.titan-embed-text-v1"
}
```
| Event  |Executing function: succeeded |
|---|---|
|![Diagram](imagens/event_3_image.jpg)|![Diagram](imagens/result_3_image.jpg)|
|||

> 💡 The next step is to take the `image_path` value and download the file from Amazon S3 bucket with a [download_file boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3-example-download-file.html) method. 

### 🚀 Let's build!

**Step 1:  APP Set Up**

✅ Clone the repo

```
git clone https://github.com/build-on-aws/langchain-embeddings
```

✅ Go to: 

```
cd serveless-embeddings
```

- Configure the [AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)

- Deploy architecture with CDK [Follow steps:](/private-assistant/README.md)

**Step 2: Deploy architecture with CDK.**

✅ Create The Virtual Environment: by following the steps in the [README](/private-assistant/README.md)

```
python3 -m venv .venv
```

```
source .venv/bin/activate
```
for windows: 

```
.venv\Scripts\activate.bat
```

✅ Install The Requirements:

```
pip install -r requirements.txt
```

✅ Synthesize The Cloudformation Template With The Following Command:

```
cdk synth
```

✅🚀 The Deployment:

```
cdk deploy
```

**Clean the house!:**

If you finish testing and want to clean the application, you just have to follow these two steps:

1. Delete the files from the Amazon S3 bucket created in the deployment. 
2. Run this command in your terminal:

```
cdk destroy
```

## Conclusion:

In this post, you built a powerful multimodal search engine capable of handling both text and images using Amazon Titan Embeddings, Amazon Bedrock, and LangChain. You generated embeddings, stored the data in FAISS vector databases, and developed applications for semantic text and image search.

Additionally, you deployed a serverless application using AWS CDK with Lambda Functions to integrate embedding and retrieval capabilities through events, providing a scalable solution.

Now you have the tools to create your own multimodal search engines, unlocking new possibilities for your applications. Explore the code, experiment, and share your experiences in the comments.

## 🚀 Some links for you to continue learning and building:

- [Building with Amazon Bedrock and LangChain](https://catalog.workshops.aws/building-with-amazon-bedrock/en-US)
- [How To Choose Your LLM](https://community.aws/posts/how-to-choose-your-llm)
- [Working With Your Live Data Using LangChain](https://community.aws/posts/working-with-your-live-data-using-langchain)


----

## 🇻🇪🇨🇱 ¡Gracias!

---

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

