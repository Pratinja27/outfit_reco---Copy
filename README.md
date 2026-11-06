# 👗 Outfit Recommendation System

A Flask web app that suggests outfits based on a user's **height, weight, gender, body shape and occasion/theme**. Users sign in, fill out a short form, and get matching outfit cards with a description and image.

---

## 📑 Table of Contents

1. [Features](#-features)
2. [Tech Stack](#-tech-stack)
3. [Project Structure](#-project-structure)
4. [How It Works](#-how-it-works)
5. [Installation & Setup](#-installation--setup)
6. [Usage](#-usage)
7. [Routes](#-routes)
8. [Dataset Format](#-dataset-format)
9. [Default Login Credentials](#-default-login-credentials)
10. [Known Limitations](#-known-limitations)
11. [Future Improvements](#-future-improvements)
12. [Security Notes](#-security-notes)

---

## ✨ Features

- 🔐 **Login system** – session-based authentication using a CSV user database
- 📝 **Preference form** – height (cm), weight (kg), gender, body shape, theme
- 🎯 **Rule-based filtering** – matches outfits by gender, body shape, theme and height/weight ranges
- 🖼️ **Recommendation page** – outfit name, description and image for each match
- 💬 **Flash messages** – feedback for login success/failure, missing fields, etc.
- 🚪 **Logout** – clears the session
- 🎨 **Styled UI** – Montserrat font, blurred glass-style cards, background images

---

## 🛠 Tech Stack

| Layer      | Technology                              |
|------------|------------------------------------------|
| Backend    | Python 3, Flask                          |
| Data       | Pandas, CSV files                        |
| Frontend   | HTML5, CSS3, Jinja2 templates            |
| Fonts/Icons| Google Fonts (Montserrat), Font Awesome  |

---

## 📁 Project Structure

```
outfit_reco/
│
├── app.py                      # Main Flask application (routes + logic)
│
├── dataset/
│   ├── users.csv               # Login credentials (username, password)
│   ├── fits.csv                # Original / older outfit dataset (~32 rows)
│   └── fits_updated.csv        # Active outfit dataset (~137 rows) ← used by app
│
├── static/
│   ├── image.png               # Static images
│   ├── kurta.png
│   └── pajama hourglass.png
│
└── templates/
    ├── sign-in.html            # Login page
    ├── index.html              # Preference input form
    └── recommendation.html     # Results page
```

---

## ⚙️ How It Works

```
 ┌──────────┐    ┌───────────┐    ┌─────────────────┐    ┌──────────────────┐
 │ /signin  │ -> │  /index   │ -> │ /recommendation │ -> │ Outfit cards     │
 │ (login)  │    │ (form)    │    │ (filter CSV)    │    │ (name, desc, img)│
 └──────────┘    └───────────┘    └─────────────────┘    └──────────────────┘
```

1. **Login** – the user enters a username and password. `load_users()` reads `dataset/users.csv` into a dictionary (`username.lower() -> password`) and checks the credentials.
2. **Form** – after login, the user enters height, weight, gender, body shape and theme. Values are validated (all fields required) and saved in `session["outfit_preferences"]`.
3. **Recommendation** – `get_recommendations()` loads `dataset/fits_updated.csv` and keeps only rows where:
   - `Gender` equals the selected gender (case-insensitive)
   - `Body Shape` equals the selected body shape (case-insensitive)
   - `Theme` equals the selected theme (case-insensitive)
   - `Height Min ≤ height ≤ Height Max`
   - `Weight Min ≤ weight ≤ Weight Max`
4. **Display** – matching rows are passed to `recommendation.html` and rendered as cards. If nothing matches, "No matching outfits found." is shown.

### Core functions in `app.py`

| Function | Purpose |
|----------|---------|
| `load_users()` | Reads `users.csv`, validates columns, returns `{username: password}` |
| `get_recommendations(height, weight, gender, body_shape, theme)` | Loads the outfit CSV, validates columns and numeric input, applies filters, returns a list of dicts |

---

## 🚀 Installation & Setup

### Prerequisites
- Python 3.8+
- pip

### Steps

```bash
# 1. Clone or extract the project
cd outfit_reco

# 2. (Optional) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install flask pandas

# 4. Run the app
python app.py
```

The app starts at **http://127.0.0.1:5000/**

> ⚠️ Run the command from the project root folder, because the CSV paths (`dataset/...`) are relative.

---

## 💡 Usage

1. Open `http://127.0.0.1:5000/` – you are redirected to the sign-in page.
2. Log in with a valid account (see below).
3. Fill in your **height, weight, gender, body shape and theme**.
4. Click **Get Recommendations**.
5. Browse the suggested outfits, or click **Go Back** to try different inputs.

---

## 🌐 Routes

| Route             | Methods     | Description                                         |
|-------------------|-------------|-----------------------------------------------------|
| `/`               | GET         | Redirects to `/signin`                              |
| `/signin`         | GET, POST   | Login page and credential check                     |
| `/index`          | GET, POST   | Preference form (requires login)                    |
| `/recommendation` | GET         | Shows matching outfits (requires login + preferences)|
| `/logout`         | GET         | Clears session and redirects to sign-in             |

---

## 🗂 Dataset Format

`dataset/fits_updated.csv` must contain these columns:

| Column        | Type   | Example                                  |
|---------------|--------|------------------------------------------|
| `Outfit`      | text   | `Saree`                                  |
| `Gender`      | text   | `Female` / `Male` / `Unisex`             |
| `Body Shape`  | text   | `Hourglass`, `Pear`, `Rectangle`, ...    |
| `Theme`       | text   | `Wedding`, `Party`, `Casual`, ...        |
| `Height Min`  | number | `150` (cm)                               |
| `Height Max`  | number | `190` (cm)                               |
| `Weight Min`  | number | `40` (kg)                                |
| `Weight Max`  | number | `98` (kg)                                |
| `Image URL`   | text   | Link to an outfit image                  |
| `Description` | text   | Short description of the outfit          |

### Dataset summary (`fits_updated.csv`)

- **137 outfit entries**
- **Genders:** Female (72), Male (58), Unisex (7)
- **Body shapes:** Rectangle, Hourglass, Pear, Oval, Inverted Triangle, Apple, Any
- **Themes:** Casual, Wedding, Party, Festival, Business, Athleisure, Summer, Edgy, Formal, Beach
- **Outfit types:** Saree, Sherwani, Kurta Pajama, Maxi Dress, Cocktail Dress, Blazer, Tracksuit, Denim Jacket, Leather Jacket, Hoodie & Joggers, Lehenga, Evening Gown, Jumpsuit, and more

### Adding a new outfit
Append a row to `dataset/fits_updated.csv`. Wrap text containing commas in double quotes.

---

## 🔑 Default Login Credentials

Stored in `dataset/users.csv`:

| Username | Password |
|----------|----------|
| `admin`  | `1234`   |
| `user1`  | `pass1`  |

To add a user, add a new line: `username,password`.

---

## ⚠️ Known Limitations

These are issues found while reviewing the code and data:

1. **Gender mismatch** – the form only offers *Male* and *Female*, but the dataset also has `Unisex` rows, which are never matched because the filter uses exact equality.
2. **"Any" body shape is never matched** – 27 rows use `Body Shape = Any` (e.g. Jeans & T-Shirt, Tracksuit), but the filter only matches the exact selected shape.
3. **Theme mismatch** – the form lists `Winter`, which has **no** rows in the dataset, while dataset themes like `Business`, `Athleisure`, `Summer`, `Edgy` are missing from the form.
4. **Inconsistent casing / typos in data** – e.g. `pear`, `hourglass`, `formal`, `Festive`. Case differences are handled, but `Festive` ≠ `Festival`.
5. **Outfit-name inconsistencies** – e.g. `Hoodie and Joggers` vs `Joggers and Hoodie`.
6. **`fits.csv` is not used** – the app reads only `fits_updated.csv`; `fits.csv` is also malformed (line 22 has an extra column) and fails to load with pandas.
7. **Static images unused** – `static/*.png` files are not referenced in any template.
8. **Passwords stored in plain text** and the Flask `secret_key` is hard-coded.
9. **No registration, no password reset** – users must be added to the CSV manually.
10. **CSV is reloaded on every request** – fine for small data, inefficient at scale.
11. **Required-column check runs before column names are stripped**, so a header with stray spaces would be rejected.

---

## 🔮 Future Improvements

- [ ] Handle `Unisex` outfits for all genders and `Any` body shape as a wildcard
- [ ] Align form options (themes, genders) with the dataset
- [ ] Add a sign-up page and store hashed passwords (`werkzeug.security`)
- [ ] Use a real database (SQLite / PostgreSQL) instead of CSV
- [ ] Calculate BMI and suggest body shape automatically
- [ ] Add filters for skin tone, budget, season and colour preferences
- [ ] Show flash messages in the templates (currently flashed but not rendered)
- [ ] Add "favourites" / save-outfit feature
- [ ] Rank results by closeness of fit instead of strict filtering
- [ ] Add unit tests for `get_recommendations()`
- [ ] Deploy to Render / Railway / Heroku with `gunicorn`

---

## 🔒 Security Notes

Before deploying to production:

- Replace `app.secret_key = "your_secret_key"` with a strong random value from an environment variable
- Hash passwords instead of storing plain text
- Turn off `debug=True`
- Use HTTPS and a production WSGI server (e.g. `gunicorn`)

---

## 📜 License

This project is for educational purposes. Add your preferred license (e.g. MIT) here.

## 👤 Author

*Your Name* – add your name / GitHub / contact here.
