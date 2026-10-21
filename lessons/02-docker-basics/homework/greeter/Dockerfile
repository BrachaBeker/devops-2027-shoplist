#slim גרסה רזה של פייתון
FROM python:3.11-slim
#קובע את תקיית העבודה בקונטיינר
WORKDIR /app
#מעתיקים קודם את קובץ הפקודות כדי שלא יצטרכו בכל פעם התקנה
COPY requirements.txt .
#מתקין את הפקודות בלי לשמור
RUN pip install --no-cache-dir -r requirements.txt
#מעתיק אחרי בכל שינוי בapp מתחיל מהשורה הזן
COPY app.py .
#הפורט שבקשו
EXPOSE 8000
#הפקודה שמריצה כשהקונטיינר עולה
CMD ["python","app.py"]
