## תיאור המערכת 

זוהי מערכת לניהול של ספרים וחברי ספריה ע"י שרת API באמצעות FastAPI שמתחבר למסד נתונים MySQL.
במסד נתונים יש שתי טבלאות:
book
members


## הקוד ליצירת docker עם MySql:

~~~
docker run --name library-mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=library_db -p 3306:3306 -v library_data:/var/lib/mysql -d mysql:latest
~~~




## מבנה התיקיות:


library-api/  
│  
├── app/  
│   ├── main.py  
│   ├── database/  
│   │   ├── db\_connection.py  
│   │   ├── book\_db.py  
│   │   └── member\_db.py  
│   ├── routes/  
│   │   ├── book\_routes.py  
│   │   ├── member\_routes.py  
│   │   └── report\_routes.py  
│   └── logs/  
│       └── app.log  
│  
├── README.md  
├── requirements.txt  
└── .gitignore


## מבנה הטבלאות:


### טבלת `books` — שדות

- ID = מפתח ראשי\
- title = כותרת הספר\
- authot = שם המחבר\
- genre = ז'אנר\
- is_available = האם הספר זמין להשאלה\
- borrowed_by_member_id = מזהה החבר שמחזיק את הספר
### טבלת `members` — שדות

- ID = מפתח ראשי\
- name = שם החבר\
- email = כתובת מייל\
- is_active = האם החבר פעיל\
- total_borrows = כמות ספרים שהושאלו ע"י החבר


## חוקי מערכת

| חוק | נושא | הכלל |
| ----: | ----: | ----: |
| 1 | יצירת ספר | המשתמש שולח title/author/genre |
| 2 | genre | חייב להיות Fiction / Non-Fiction / Science / History / Other|
| 3 | יצירת חבר | המשתמש שולח name/email|
| 4 | email | חייב להיות ייחודי|
| 5 | חבר לא פעיל | אם `is_active=False` — אי אפשר להשאיל ספר |
| 6 | ספר לא זמין | אי אפשר להשאיל ספר שכבר מושאל|
| 7 | מקסימום ספרים | חבר לא יכול להחזיק יותר מ-3 ספרים בו-זמנית |
| 8 | החזרת ספר | ניתן להחזיר ספר רק אם הוא מושאל לאותו חבר שמחזיר אותו |


## Endpoints

### Books

| Method | Endpoint | תיאור |
| :---- | :---- | :---- |
| `POST` | `/books` | יצירת ספר |
| `GET` | `/books` | הצגת כל הספרים |
| `GET` | `/books/{id}` | הצגת ספר לפי ID |
| `PATCH` | `/books/{id}` | עדכון ספר |
| `PATCH` | `/books/{id}/borrow/{member_id}` | השאלת ספר לחבר |
| `PATCH` | `/books/{id}/return/{member_id}` | החזרת ספר מחבר |

### Members

| Method | Endpoint | תיאור |
| :---- | :---- | ----: |
| `POST` | `/members` | יצירת חבר |
| `GET` | `/members` | הצגת כל החברים |
| `GET` | `/members/{id}` | הצגת חבר לפי ID |
| `PATCH` | `/members/{id}` | עדכון חבר |
| `PATCH` | `/members/{id}/deactivate` | השבתת חבר |
| `PATCH` | `/members/{id}/activate` | הפעלת חבר |

### Reports

| Method | Endpoint | תיאור |
| :---- | :---- | ----- |
| `GET` | `/reports/summary` | הצגת דוח כללי |
| `GET` | `/reports/books-by-genre` | הצגת ספרים לפי ז'אנר |
| `GET` | `/reports/top-member` | הצגת החבר הכי פעיל |

### `/reports/summary`

מחזיר:

- מספר ספרים כולל  
- מספר ספרים זמינים  
- מספר ספרים מושאלים כרגע  
- מספר חברים פעילים

דוגמה:  
{  
  "total\_books": 0,  
  "available\_books": 0,  
  "currently\_borrowed": 0,  
  "active\_members": 0  
}

### `/reports/books-by-genre`

דוגמה למה שמחזיר:  
\[  
{"Genre": "Science", "COUNT": 3},  
{"Genre": "History", "COUNT": 2}  
\]

### `/reports/top-member`

דוגמה למה שמחזיר:

{  
  "member\_id": 1,  
  "borrowed": 5  
}

## 

## זרימת המערכת:

ברגע עליית השרת נוצרות ב- database שתי טבלאות \
(אם לא קיימות)\
⭣\
אפשרויות:\
ניהול ספרים\
ניהול חברים\
ניהול כללי\
⭣\
ניהול ספרים:\
יצירת ספר\
הצגת כל הספרים\
הצגת ספר לפי ID\
עריכת ספר\
השאלת ספר\
החזרת ספר\
⭣\
ניהול חברים:\
יצירת חבר חדש\
הצגת כל החברים\
הצגת חבר לפי ID\
עריכת חבר\
השבתת חבר\
הפעלת חבר\
⭣\
ניהול כללי:\
הצגת דוח כללי\
הצגת ספרים לפי ז'אנר\
הצגת החבר הכי פעיל

## הוראות הרצה:
לאחר הפעלת הקונטיינר (ראה פקודה למעלה)
### התקנת ספריות:
~~~
pip install -r requirements.txt
~~~

### הרצת התוכנית:
~~~
python main.py
~~~