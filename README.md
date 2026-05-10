oke paham, jadi caption dan semua teks README pake english, tapi di bagian "Interface Context" lo tetap jelasin dalam english apa yang user ketik dalam bahasa indonesia di screenshot-nya. gas:

---

# Charlotte: Smart Inventory & Market Analyst Bot 🤖📦

Most inventory systems force you to open an app, fill a form, and click through menus just to log incoming stock. I built Charlotte to eliminate all of that — a Telegram bot that understands natural language, reads photos, and syncs everything to Google Sheets automatically. No forms. No manual entry. Just chat.

Built with n8n, Google Gemini AI (multimodal), and Google Sheets as the backend database.

---

## 🛠️ How It Works (The Workflows)

The system runs on two parallel pipelines triggered from a single Telegram entry point. An AI Agent at the front parses every incoming message and routes it to the right pipeline automatically.

### 1. Inventory Input Pipeline

A Telegram bot integrated with Gemini AI and Google Sheets to handle all stock management.

![Inventory Input Workflow](canvas_input.png)

- **Natural Language Input:** Send stock updates in any format — no fixed template required. The AI extracts all fields automatically.
- **Smart Follow-Up:** If required fields are missing, the bot detects which ones are empty and asks specifically for what's needed. It remembers conversation context across multiple messages.
- **New vs. Existing Product Detection:** Before saving, the system checks whether the product already exists in the database. If it does, it updates the stock. If it's new, it creates a fresh entry.
- **Stock History Logging:** Every stock change is automatically logged to a separate `Stock_History` sheet with a timestamp, old stock, new stock, and change type.

### 2. Market Analysis Pipeline

A second AI agent that acts as a business analyst when given competitor screenshots.

![Market Analysis Workflow](canvas_analysis.png)

- **Competitor Price Extraction:** Send a screenshot of any product listing from Shopee, Tokopedia, or other marketplaces. The AI reads the product name, market price, rating, and sales volume directly from the image.
- **Internal Price Cross-Reference:** The system automatically pulls the product's cost price and current selling price from your own database to compare against market data.
- **Business Insight Generation:** Calculates price gap, margin percentage at current price vs. market price, and generates a recommended competitive price range with reasoning.
- **Restock Recommendation:** Based on demand signals (rating, units sold) and margin potential, the bot delivers a concrete buy/hold recommendation.

---

## 📸 Interface Context

The bot interface is in Indonesian, as it's designed for daily use by the end user. Here's what each screenshot demonstrates:

**Image 1 — Natural Language Stock Input:**
The user sends *"bro stok gula pasir masuk 20kg hari ini ya"* ("hey, 20kg of sugar just came in today"). No format required. The bot detects that price data is missing, asks for it, and confirms the save once answered.

![Stock Input](input_text.png)

**Image 2 — Single-Line Complete Input:**
The user sends *"stok minyak goreng masuk 10 liter, modal 14000 jual 16000"* ("cooking oil stock in: 10 liters, cost 14000, sell 16000"). All fields provided in one message — no follow-up needed, bot saves immediately.

![Single Line Input](input_complete.png)

**Image 3 — Restock Existing Product:**
The user sends *"bang, stok minyak goreng masuk lagi 5 liter"* ("hey, another 5 liters of cooking oil just came in"). The bot recognizes this as an update to an existing product and increments the stock accordingly.

![Restock](input_restock.png)

**Image 4 & 5 — Market Analysis from Competitor Screenshot:**
The user sends a Shopee screenshot of Rose Brand Cooking Oil listed at Rp 45,125 with 519 units sold and a 4.8 rating. The bot cross-references the internal database (cost price: Rp 14,000), calculates margin scenarios at current vs. market price, and delivers a pricing recommendation with a buy/hold decision.

![Market Analysis](analysis_result.png)

**Image 6 — Products Database (Google Sheets):**
The live `products_clean` sheet showing all inventory with auto-calculated margin % and stock value columns. All data is written directly by n8n — zero manual entry.

![Products Sheet](sheet_products.png)

**Image 7 — Stock History Log:**
The `stock_history` sheet showing every stock movement with timestamp, product name, old/new stock, difference, and change type (`input_baru` = new entry, `update_stok` = restock).

![Stock History](sheet_history.png)

---

## ⚙️ Architecture

- **n8n** handles all deterministic logic: routing, null-field validation, read/write to Google Sheets, and sending Telegram replies.
- **AI Agent (Input)** handles interpretation: extracting structured data from unstructured text or images, detecting missing fields, generating specific follow-up questions.
- **AI Agent (Analysis)** is a separate specialist: only runs on market analysis requests, receives trimmed internal + external data, outputs business insights.
- **Fallback Model** configured on both agents — if the primary Gemini model hits rate limits, a secondary model takes over automatically with zero downtime.

---

## 💻 Tech Stack

| Layer | Tool |
|---|---|
| Workflow Automation | n8n |
| Chat Interface | Telegram Bot API |
| AI Vision & NLP | Google Gemini Pro (Multimodal) |
| Database | Google Sheets API |
| Fallback AI | Google Gemini Flash |

---

## 📁 Database Schema

**`Products` sheet** — snapshot of current inventory

| Column | Type | Required |
|---|---|---|
| nama_produk | String | ✅ |
| stok | Number | ✅ |
| satuan | String | ✅ |
| harga_modal | Number | ✅ |
| harga_jual | Number | ✅ |
| margin_persen | Formula (auto) | — |
| nilai_stok | Formula (auto) | — |
| foto_url | String | ❌ |
| deskripsi | String | ❌ |
| last_updated | Timestamp | Auto |

**`Stock_History` sheet** — full timeline of stock changes

| Column | Type |
|---|---|
| timestamp | DateTime |
| nama_produk | String |
| stok_lama | Number |
| stok_baru | Number |
| selisih | Formula (auto) |
| jenis_perubahan | `input_baru` / `update_stok` |

---

## 🗺️ Roadmap

- [x] Natural language stock input
- [x] Multi-turn conversation with context memory
- [x] New vs. existing product detection
- [x] Stock history logging
- [x] Competitor market analysis from screenshot
- [x] Fallback AI model on rate limit
- [ ] Stock query — *"how much cooking oil is left?"*
- [ ] Low stock alert — auto-notify when stock drops below threshold
- [ ] Photo input for physical products
- [ ] Monthly/weekly performance summary

---

tinggal lo rename foto-fotonya sesuai placeholder yang ada, terus copy paste ini ke `README.md` di repo GitHub lo. next, LinkedIn post?
