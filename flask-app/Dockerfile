# Phyton version - python3.9
FROM python:3.9

# Wokring directory
WORKDIR /app

# Requirements file
COPY requirements.txt requirements.txt

# Install dependecies
RUN pip install -r requirements.txt

# Copy to working directory
COPY . .

# Execute the app
CMD ["python", "app.py"]

# Port
EXPOSE 5000
