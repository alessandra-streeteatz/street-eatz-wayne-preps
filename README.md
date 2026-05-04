# Street Eatz — Wayne Preps Backend

Flask + SQLite backend for the vendor store microsite.

## Quick Start

```bash
# Install (only Flask needed — comes with Python)
pip install flask

# Start the server
python app.py
```

Then open `index.html` in a browser (or serve it from the same Flask app).

---

## API Reference

### Vendor
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/vendor` | Get vendor profile |
| PUT | `/api/vendor` | Update vendor profile (name, phone, hours, etc.) |
| GET | `/api/vendor/status` | Get open/closed status |
| PUT | `/api/vendor/status` | Toggle open/closed `{"is_open": true}` |

### Menu
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/menu` | All menu items |
| POST | `/api/menu` | Create item `{name, price, description?, category?, tag?}` |
| PUT | `/api/menu/:id` | Update item |
| DELETE | `/api/menu/:id` | Delete item |

### Orders
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/orders` | Place order `{customer_name, customer_phone, pickup_time, items: [{id, qty}]}` |
| GET | `/api/orders` | List all orders (vendor dashboard) |
| GET | `/api/orders/:id` | Single order |
| PUT | `/api/orders/:id/status` | Update status `{status: "confirmed"|"ready"|"picked_up"|"cancelled"}` |

### Checkout (Stripe-ready)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/checkout/initiate` | Validate cart server-side, returns authoritative totals. Stripe session creation is pre-wired — just add keys. |

### Uploads
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/uploads` | Upload photo (multipart/form-data, field: `file`) |
| GET | `/api/uploads/:filename` | Serve uploaded photo |

---

## Order Statuses
`pending` → `confirmed` → `ready` → `picked_up`  
Any status can transition to `cancelled`.

---

## Adding Stripe Payments

1. `pip install stripe`
2. Set env var: `export STRIPE_SECRET_KEY=sk_live_...`
3. In `app.py`, find the `checkout_initiate` endpoint and uncomment the Stripe block.
4. Update the `success_url` and `cancel_url` to your domain.
5. In `index.html`, after calling `/api/checkout/initiate`, if `stripe_ready: true`, redirect to `result.stripe_url`.

---

## Database

SQLite file: `street_eatz.db` — auto-created on first run. Seed data (Wayne Preps menu) is inserted on first run only.

---

## Deploying

This is a standard Flask app. Works on:
- **Railway / Render / Fly.io** — push the folder, set start command to `python app.py`
- **VPS** — run behind nginx with gunicorn: `gunicorn app:app -w 4 -b 0.0.0.0:5000`
- Change `API_BASE` in `index.html` to your production URL when deploying.
