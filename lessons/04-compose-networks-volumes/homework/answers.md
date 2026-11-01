# סיכום תשובות -- Docker

## 1. איך stats מגיע ל-backend ואיך הדפדפן מגיע אליו?

שירותים ב-Docker Compose מתקשרים דרך רשת פנימית באמצעות שם השירות, למשל
`backend:5000`, בלי צורך ב-`ports`. הדפדפן מגיע ל-backend דרך Nginx:
הוא פונה אל `localhost:8080/api/`, ו-Nginx מעביר את הבקשה ל-backend
(Reverse Proxy).

## 2. מה קורה לנתונים ב-`down` וב-`down -v`?

אחרי `docker compose down` ו-`up` המוצרים נשארים, כי `down` מוחק את
הקונטיינרים אבל לא את ה-Named volume `postgres-data`, שבו שמורים הנתונים.
`down -v` מוחק גם את ה-volume, ולכן המוצרים נמחקים. קבצים ב-Bind mounts,
כמו `init.sql`, לא נמחקים.

## 3. מה סוג ה-volume של `init.sql` ומה קורה כשמשנים אותו?

זה Bind mount, כי המקור הוא נתיב לקובץ במחשב המארח. שינוי הקובץ לא מריץ
את הסקריפט מחדש אם המסד כבר נוצר, כי סקריפטי האתחול רצים רק ביצירת מסד
ריק.

## 4. מה שם הרשת וה-volume?

הרשת היא `docker_default` וה-volume הוא `docker_postgres-data` (לפי
`docker network ls` ו-`docker volume ls`). שם הפרויקט הוא `docker`, לפי שם
התיקייה של קובץ ה-Compose. `default` הוא שם רשת ברירת המחדל של Compose,
ו-`postgres-data` הוא השם שהוגדר תחת `volumes` בקובץ.

## 5. מה יש ב-image הסופי של Multi-stage ולמה אין CMD?

ה-image הסופי מכיל את Nginx, את `nginx.conf` ואת תוצרי הבנייה מ-`dist`, ללא
Node.js וללא `build.js`. אין `CMD` כי הוא יורש את פקודת ההרצה מ-image הבסיס של
Nginx. ל-stats לא צריך Multi-stage כי אין לו שלב בנייה, ו-Node נדרש לו גם בזמן
הריצה, אז אין מה להשאיר מאחור.

## 6. למה `multi` כמעט בגודל של `single`?

שניהם בנויים על אותו image של Nginx ומוסיפים רק כמה קבצים קטנים של האתר, ולכן
רוב הגודל הוא Nginx עצמו. Node.js נמצא רק ב-`kitchen`, ולכן הוא הגדול, והוא לא
עובר ל-`multi`.

- single: 92.8MB
- multi: 92.7MB
- kitchen: 244MB

## 7. למה `/api/stats` מגיע ל-stats ומה קורה ל-`/api/products`?

הבקשה מתאימה לשני ה-locations, ו-Nginx בוחר את ה-prefix הארוך ביותר:
`location /api/stats` ארוך יותר מ-`location /api/`, ולכן הבקשה מגיעה ל-stats.
כאשר `proxy_pass` מסתיים ב-`/`, החלק `/api/` מוחלף בלוכסן, והנתיב
`/api/products` מגיע ל-backend כ-`/products`.




