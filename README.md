# Streamlit App Deployment

This project contains a Streamlit app deployed on Google Cloud Run.

## Instructions

1. Set up your Python virtual environment:

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

2. Install dependencies:

    ```bash
    pip install -r requirements.txt
    ```

3. Build and run the app locally:

    ```bash
    streamlit run your_script_name.py
    ```

4. Build and push Docker image:

    ```bash
    docker build -t gcr.io/your-project-id/your-image-name .
    docker push gcr.io/your-project-id/your-image-name
    ```

5. Deploy on Google Cloud Run:

    ```bash
    gcloud run deploy --image gcr.io/your-project-id/your-image-name --platform managed
    ```

6. Access your app on Cloud Run.

For more details, refer to the [official documentation](https://docs.streamlit.io/) and [Google Cloud Run documentation](https://cloud.google.com/run/docs).
