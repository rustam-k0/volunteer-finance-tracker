### 🚀 About the Project

**HvostatyeSosediBot** is a tool developed to simplify financial accounting for animal assistance projects. It allows for the rapid recording of **Income** (donations) and **Expenses** (pet supplies, veterinary services), minimizing manual data entry.

The bot utilizes **Google Cloud Vision OCR** technology to extract key information—including date, amount, and bank name—from photos of receipts and banking screenshots.

---

### ✨ Core Functionality

* **Ingestion & OCR:** Accepts financial documents as photos, extracts text, and automatically parses the date, transaction amount, and bank.
* **User Dialogue:** Uses inline buttons for context selection (assigning the transaction to a specific pet and classifying it as "Income" or "Expense").
* **Manual Correction:** Displays all recognized data for user verification before saving. Users can correct any field step-by-step if the automation fails.
* **Google Sheets Integration:** Automatically appends the validated record to a Google Sheet tab corresponding to the pet's name.

---

### 🤖 Workflow Scenario

1. **Initialization:** User sends `/start`.
2. **Context Selection:** Bot requests the operation type (`Income`/`Expense`) and the Pet Name.
3. **Image Upload:** User uploads a photo of a physical receipt or a transfer screenshot.
4. **Automated Processing:**
* Bot sends the image to **Google Cloud Vision**.
* Internal service `data_parser.py` analyzes the raw text to extract `date`, `amount`, `author`, and attempts to identify the `bank`.


5. **User Interaction:**
* **Scenario A (Bank Found):** If the bank is successfully recognized, the bot proceeds directly to final confirmation.
* **Scenario B (Bank Not Found):** If parsing fails, the bot displays the recognized date/amount and prompts the user to select a bank from a list (`Sber`, `T-Bank`, `Other...`).


6. **Final Confirmation:** Bot presents the aggregated data (auto-parsed + manual inputs) for review.
7. **Save:** Upon clicking "Save", data is committed to Google Sheets.

---

### 📊 Data Structure

#### Google Sheets Layout

A separate sheet is created for each pet in the master spreadsheet.

| INCOME (Donations) |  |
| --- | --- |
| `Date`, `Amount`, `Bank`, `Author`, `Comment` |  |
| **EXPENSE (Bills)** |  |
| `Date`, `Amount`, `Procedure`, `Author`, `Comment` |  |

#### Code Variables

* **Date:** `date` (string/date)
* **Amount:** `amount` (float)
* **Bank:** `bank` (string)
* **Author:** `author` (string)
* **Comment:** `comment` (string)
* **Type:** `type` (string: `income` / `expense`)
* **Procedure:** `procedure` (string)

> **Field Entry Rules:**
> * **Author (`author`):**
> * *Expense:* Name of clinic, shop, or vendor (e.g., "MedLab").
> * *Income:* Name of the donor (e.g., "Denis L.").
> 
> 
> * **Comment (`comment`):**
> * *Expense:* Purpose of payment (e.g., "Royal Canin Food" or "Vaccination").
> * *Income:* Message from the donor (e.g., "Treats for Casper!").
> 
> 
> 
> 

#### JSON Example (for pet "Casper")

```json
// Type: Income
{
  "date": "15.01.2025",
  "amount": 500.0,
  "bank": "Sberbank",
  "author": "Denis L.",
  "comment": "Treats for Casper!"
}

// Type: Expense
{
  "date": "15.01.2025",
  "amount": 1580.5,
  "procedure": "Blood Analysis",
  "author": "MedLab",
  "comment": "Discount applied"
}

```

---

### 🛠 Tech Stack

* **Backend:** Python 3.9+, FastAPI
* **Telegram API:** `python-telegram-bot`
* **OCR:** `google-cloud-vision`
* **Tables:** `gspread`
* **Validation:** `Pydantic`
* **Storage:** Google Sheets
* **Hosting:** Render (or similar PAAS)

---

### 📁 Project Structure

```text
HvostatyeSosediBot/
├── app/
│   ├── bot/
│   │   ├── handlers.py      # Dialogue logic (ConversationHandler)
│   │   └── keyboards.py     # Inline keyboards
│   ├── services/
│   │   ├── vision_ocr.py    # Google Cloud Vision client
│   │   ├── sheets_client.py # Google Sheets client
│   │   └── data_parser.py   # Text extraction logic
│   └── models/
│       └── schemas.py       # Pydantic data schemas
├── config/
│   └── settings.py          # Configuration and Environment variables
├── requirements.txt         # Project dependencies
├── main.py                  # FastAPI entry point
├── credentials.json         # Google Service Account Key
└── .env                     # Environment variables file

```

---

### ⚙️ Installation & Run

1. **Clone the repository:**
```bash
git clone [your_repo_url]
cd HvostatyeSosediBot

```


2. **Create and activate virtual environment:**
```bash
python3 -m venv venv
source venv/bin/activate

```


3. **Install dependencies:**
```bash
pip install -r requirements.txt

```


4. **Run the application:**
```bash
uvicorn main:app --reload

```
