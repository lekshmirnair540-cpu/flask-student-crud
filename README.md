# Student Manager – Flask CRUD Application

A mini Flask web application for managing student records (Create, Read,
Update, Delete), built for the **Learn Beyond (LB) Activity – Part B**.

It uses secure, hashed-password authentication (the same concept studied
for **Part A: Password Hashing**) to protect the CRUD operations — only a
logged-in user can view or manage student records.

## Features / Flask Concepts Used

| Concept | Where it's used |
|---|---|
| Flask Routing | `app/routes.py` |
| HTML Templates | `app/templates/` |
| Jinja2 Template Engine | Template inheritance (`base.html`), loops, conditionals |
| Static Files (CSS) | `app/static/css/style.css` |
| Flask-WTF Forms | `app/forms.py` (registration, login, student form) with CSRF protection |
| Database Integration (Flask-SQLAlchemy) | `app/models.py` (`User`, `Student`) |
| Flask-Migrate | `migrations/` folder, Alembic-based schema migrations |
| Password Hashing (Part A topic) | `User.set_password()` / `User.check_password()` using Werkzeug's `generate_password_hash` / `check_password_hash` |
| Session-based Authentication | Flask-Login (`login_user`, `login_required`, `current_user`) |
| Pagination | `/students` list view, 5 records per page |
| CRUD Operations | Add / List / View / Edit / Delete student records |

## Project Structure

```
flask_student_crud/
├── app/
│   ├── __init__.py        # Application factory, extension setup
│   ├── models.py           # User and Student database models
│   ├── forms.py             # Flask-WTF form definitions
│   ├── routes.py            # All route handlers (auth + CRUD)
│   ├── templates/
│   │   ├── base.html         # Shared layout, navbar, flash messages
│   │   ├── login.html
│   │   ├── register.html
│   │   └── students/
│   │       ├── list.html      # Paginated student list
│   │       ├── form.html      # Shared Add/Edit form
│   │       └── view.html      # Single student detail view
│   └── static/
│       └── css/
│           └── style.css
├── migrations/               # Flask-Migrate / Alembic migration files
├── config.py                  # App configuration (secret key, DB URI)
├── run.py                     # Application entry point
├── requirements.txt
├── .flaskenv
├── .gitignore
└── README.md
```

## Database Model

**User**
- `id`, `username` (unique), `password_hash`, `created_at`
- Passwords are never stored in plain text — `generate_password_hash()`
  creates a salted hash on registration, and `check_password_hash()`
  verifies it on login.

**Student**
- `id`, `roll_number` (unique), `name`, `email` (unique), `department`,
  `year`, `phone`, `created_at`, `updated_at`

## Application Workflow

1. A new user **registers** an account (password is hashed before saving).
2. The user **logs in**; Flask-Login creates a session for them.
3. All `/students/*` routes are protected with `@login_required` — an
   anonymous visitor is redirected to the login page.
4. From the **Students** page the user can:
   - View the paginated list of records
   - **Add** a new student (validated Flask-WTF form)
   - **View** a single student's full details
   - **Edit** an existing record
   - **Delete** a record (with a confirmation prompt)
5. The user can **log out**, which clears the session.

## Setup & Execution

### 1. Clone the repository
```bash
git clone <your-repo-url>
cd flask_student_crud
```

### 2. Create and activate a virtual environment
```bash
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Set up the database
The `migrations/` folder is already included in this repository, so you
only need to apply the existing migrations:
```bash
flask db upgrade
```

> If you ever change a model and need a **new** migration, use:
> ```bash
> flask db migrate -m "Description of change"
> flask db upgrade
> ```

### 5. Run the application
```bash
flask run
```
Then open **http://127.0.0.1:5000** in your browser.

### 6. Try it out
1. Go to **Register** and create an account.
2. **Log in** with that account.
3. Click **Add Student** to create a record, then browse/edit/delete it
   from the **Students** list.

## Output of Each CRUD Operation (for the viva)

- **Create** – "Add Student" form → submits → flash message "Student added
  successfully." → new row appears at the top of the list.
- **Read** – "Students" page shows a paginated table; clicking "View"
  opens a single record's detail page.
- **Update** – "Edit" pre-fills the form with existing data; on save, a
  flash message confirms the update and the table reflects the change.
- **Delete** – "Delete" prompts for confirmation, then removes the row and
  shows a flash message.

## Notes

- `SECRET_KEY` in `config.py` defaults to a development value — set the
  `SECRET_KEY` environment variable to something random in production.
- The SQLite database file (`app.db`) is intentionally excluded from
  version control via `.gitignore`; running `flask db upgrade` recreates
  it locally from the migration files.
