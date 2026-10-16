# The File Room — Complete Local Execution & Test Guide

This document explains how to correctly run both frontends and the Firebase backend locally without port collisions or browser URL errors.

---

## 1. Why Did `ERR_ADDRESS_INVALID` Occur?

When you run `python -m http.server 5500`, Python binds to the IPv6 wildcard address `[::]` and outputs:
```
Serving HTTP on :: port 5500 (http://[::]:5500/) ...
```
Windows browsers (Chrome, Edge, Brave) **do not allow navigating directly to `[::]`** and immediately display:
> **`ERR_ADDRESS_INVALID` (Hmmm... can't reach this page)**

### The Quick Fix:
Replace `[::]` in your browser URL bar with **`localhost`** or **`127.0.0.1`**:

* **Client Portal:** [`http://localhost:5500/client/login.html`](http://localhost:5500/client/login.html)
* **Firm Operations Console:** [`http://localhost:5501/login.html`](http://localhost:5501/login.html)

---

## 2. Running Both Portals (Avoiding Port Collisions)

Both portals must run on **different ports**:

### Portal 1: Client Portal (Port 5500)
Open a terminal and run:
```powershell
cd "C:\Users\user\Desktop\firm"
python -m http.server 5500 --bind 127.0.0.1
```
> **URL:** [`http://localhost:5500/client/login.html`](http://localhost:5500/client/login.html)  
> *(Dashboard: `http://localhost:5500/client/dashboard.html`)*  
> *(Adding `--bind 127.0.0.1` ensures Python generates a clickable `http://127.0.0.1:5500/` link that never throws `ERR_ADDRESS_INVALID`)*

---

### Portal 2: Firm Operations Console (Port 5501)
Open a **second** terminal and run:
```powershell
cd "C:\Users\user\Desktop\firm"
python -m http.server 5501 --bind 127.0.0.1
```
> **URL:** [`http://localhost:5501/login.html`](http://localhost:5501/login.html)  
> *(Dashboard: `http://localhost:5501/dashboard.html`)*  
> *(Note: Use port **5501** so it does not collide with the Client Portal on 5500)*

---

## 3. Running with Firebase Emulator Suite

To test rules and database queries against the local Firestore emulator:

```powershell
cd "C:\Users\user\Desktop\firm"
firebase emulators:start --only firestore
```
* **Firestore Emulator:** `127.0.0.1:8080`
* **Emulator UI Dashboard:** [`http://localhost:4000`](http://localhost:4000)

The frontends in `js/firebase.js` automatically detect `localhost` and attach to the emulator on port `8080`.

---

## 4. Running the Automated Tests

All 118 security and integration tests are passing and can be executed via:

```powershell
# Run all 118 test cases across the entire test suite
cd "C:\Users\user\Desktop\firm"
node --test tests/*.test.js

# Run the 14-criterion Master Pre-Production Verification Suite
node --test tests/final-production-integration.test.js

# Run Cloud Functions tests (24 tests)
cd "C:\Users\user\Desktop\firm\functions"
npm test
```

---

## 5. Summary of Correct URLs

| Application | Correct Browser URL | DO NOT USE |
|---|---|---|
| **Client Portal** | `http://localhost:5500/client/login.html` | ~~`http://[::]:5500/`~~ |
| **Firm Operations Console** | `http://localhost:5501/login.html` | ~~`http://[::]:5501/`~~ |
| **Emulator Dashboard** | `http://localhost:4000` | ~~`http://[::]:4000/`~~ |
