FROM python:3.9
COPY . .
RUN pip install -r requirements.txt
ENV FLASK_APP=app.py
CMD ["python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=5000"]
