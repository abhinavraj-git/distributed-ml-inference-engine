# Inference Engine

An asynchronous sentiment-inference service: clients submit text to an Express API, which records each job in PostgreSQL and publishes it to RabbitMQ; Python workers consume the queue, run a DistilBERT sentiment model on CPU, and write results back to PostgreSQL, while Redis handles rate limiting and clients poll the API for job status and results.
