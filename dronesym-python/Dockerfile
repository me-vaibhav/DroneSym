
FROM python:3.8

# Ensure Python output is unbuffered for real-time logs
ENV PYTHONUNBUFFERED=1

# Install system dependencies for dronekit-sitl
RUN apt-get update && apt-get install -y \
    libgl1-mesa-glx \
    libglu1-mesa \
    libosmesa6 \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY requirements.txt /usr/src/app
RUN pip3 install -r requirements.txt
COPY ./flask-api/src /usr/src/app
EXPOSE 5000
CMD ["python3", "main.py"]
