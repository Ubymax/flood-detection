# Use official TensorFlow image as base
FROM tensorflow/tensorflow:2.6.0

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
ENV FLASK_ENV production
ENV MODEL_DIR=/app/models
ENV UPLOAD_FOLDER=/app/uploads

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libgl1-mesa-glx \
    libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

# Create and set working directory
WORKDIR /app

# Install Python dependencies
COPY requirements.txt .
RUN pip install --upgrade pip && \
    pip install -r requirements.txt && \
    pip install gunicorn

# Copy application files
COPY . .

# Create required directories
RUN mkdir -p ${UPLOAD_FOLDER} && \
    mkdir -p ${MODEL_DIR} && \
    chmod a+rwx ${UPLOAD_FOLDER}

# Copy your trained models and artifacts (adjust paths as needed)
# Note: In production, you might want to mount these as volumes instead
COPY models/VGG16_Transfer_complete.h5 ${MODEL_DIR}/
COPY models/label_encoder.pkl ${MODEL_DIR}/
COPY models/VGG16_Transfer_architecture.json ${MODEL_DIR}/
COPY models/VGG16_Transfer_weights.h5 ${MODEL_DIR}/

# Expose the port the app runs on
EXPOSE 8000

# Command to run the application
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--workers", "4", "--timeout", "120", "--access-logfile", "-", "--error-logfile", "-", "app:app"]