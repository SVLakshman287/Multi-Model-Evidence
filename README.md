# Multi-Modal Automated Insurance Claims Verifier
[![Ask DeepWiki](https://devin.ai/assets/askdeepwiki.png)](https://deepwiki.com/SVLakshman287/Multi-Model-Evidence.git)

This repository contains an agentic multi-modal AI system designed to automatically review, analyze, and verify insurance claims for asset damage. The system processes user conversation logs, claim metadata, and visual evidence (images) to determine the authenticity of a claim, assess damage, and flag potential risks like fraud.

## Key Features

-   **Multi-Modal Analysis:** Leverages the Gemini 2.5 Flash vision-language model to simultaneously interpret textual conversations and visual evidence.
-   **Structured Output:** Employs Pydantic schemas to enforce a strict, consistent JSON output format for reliable, machine-readable results.
-   **Robust API Management:** Features an engineered smart pacing delay and an exponential backoff retry mechanism to effectively manage strict API rate limits and ensure fault tolerance.
-   **Graceful Error Handling:** Automatically populates unprocessed or failed claims with valid placeholder data, ensuring the final output is always complete and adheres to the required schema, preventing downstream processing errors.

## How It Works

The core logic is contained within the `Multi_Modal_Evidence_Review.ipynb` notebook. The pipeline executes the following steps for each claim:

1.  **Data Ingestion:** Reads claim information from `dataset/claims.csv`, which includes the user ID, a log of their conversation with an agent, the claimed object (e.g., car, laptop), and paths to the corresponding evidence images.
2.  **Prompt Engineering:** For each claim, a detailed prompt is constructed containing the contextual data (Claim ID, object type, conversation log) and instructions for the AI model.
3.  **Image Loading:** The associated evidence images are loaded from the `dataset/images/` directory.
4.  **Multi-Modal Inference:** The prompt and images are sent to the Google Gemini model. The model is configured to return a JSON object that conforms to a predefined `EvidenceReviewOutput` Pydantic schema.
5.  **Claim Evaluation:** The model analyzes the provided information to:
    -   Extract the specific damage claimed by the user.
    -   Assess if the visual evidence is sufficient.
    -   Identify the type and location of any visible damage.
    -   Make a final decision: `SUPPORTED`, `CONTRADICTED`, or `INSUFFICIENT_INFO`.
    -   Estimate the severity and provide a justification for its decision.
6.  **Output Generation:** The results are collected and saved to `output.csv`, with robust handling for API quota limits and processing errors. The script processes the data in batches to avoid being rate-limited.

## Dataset

The `dataset/` directory contains all the necessary data for running and evaluating the system.

-   `claims.csv`: The main input file containing the claims to be processed.
-   `images/`: Contains subdirectories for each claim case, holding the JPEG images provided as visual evidence.
-   `evidence_requirements.csv`: A supplementary file detailing the minimum image evidence required for various claim types.
-   `user_history.csv`: Provides past claim history for each user, which can be used for risk assessment.
-   `output.csv`: An empty template file defining the schema for the final submission.
-   `sample_claims.csv`: A small sample of claims with pre-filled evaluation data, useful for understanding the expected output format.

## Getting Started

### Prerequisites

-   Python 3.x
-   A Google Colab environment is recommended for running the notebook.
-   Required Python libraries: `google-genai`, `pandas`, `pillow`, `pydantic`.

### Setup and Execution

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/SVLakshman287/Multi-Model-Evidence.git
    cd Multi-Model-Evidence
    ```

2.  **Set up Google Colab:**
    -   Upload the `code/Multi_Modal_Evidence_Review.ipynb` notebook.
    -   Upload the `dataset` directory or a zip file containing its contents. The notebook includes a cell to unzip the data if a `claims.zip` file is used.
    -   Set your Google Gemini API key as a secret. In the Colab interface, click the key icon on the left panel and create a new secret named `GEMINI_API_KEY` or `FINAL_KEY` with your API key as the value.

3.  **Run the Notebook:**
    -   Open `Multi_Modal_Evidence_Review.ipynb` in Colab.
    -   Run the cells sequentially. The notebook will install dependencies, load the data, process the claims in batches, and generate the final `output.csv`.
    -   The final cells will trigger a browser download for the completed `output.csv` file.
