<p align="center">
  <img src="docs/banner.svg" alt="Smart & Safe: a second-hand phone marketplace that checks every listing against a stolen-IMEI registry" width="100%">
</p>

<p align="center">
  <img alt="ASP.NET Web Forms" src="https://img.shields.io/badge/ASP.NET-Web%20Forms-512BD4?logo=dotnet&logoColor=white">
  <img alt="C#" src="https://img.shields.io/badge/C%23-.NET%20Framework%204.7.2-239120?logo=csharp&logoColor=white">
  <img alt="SQL Server LocalDB" src="https://img.shields.io/badge/SQL%20Server-LocalDB-CC2927?logo=microsoftsqlserver&logoColor=white">
  <img alt="Bootstrap 5" src="https://img.shields.io/badge/Bootstrap-5.2-7952B3?logo=bootstrap&logoColor=white">
  <img alt="Font Awesome 6" src="https://img.shields.io/badge/Font%20Awesome-6.1-528DD7?logo=fontawesome&logoColor=white">
</p>

# Smart & Safe

**Smart & Safe** is a web marketplace for buying and selling used mobile phones, built to make sure **stolen phones don't get resold**.

Every phone has a unique **IMEI** number. People who lose a phone, or have one stolen, can report its IMEI pair along with their police **GD (General Diary) number**. Sellers post their phone with its IMEIs, NID scans and purchase memo. An admin reviews each ad and checks it against the stolen-phone registry. Only approved ads appear on the public Buy page.

> *"In case you buy a second-hand phone and it turns out to be stolen, you might have to hand it over to its actual owner and not get a penny back!"*
> Smart & Safe checks the IMEI for you so you never end up buying a stolen one.

---

## Table of contents

- [Features](#features)
- [See it in action (simulations)](#see-it-in-action)
- [Screenshots](#screenshots)
- [How it works](#how-it-works)
- [Pages](#pages)
- [Data model](#data-model)
- [Getting started](#getting-started)
- [Project structure](#project-structure)
- [Known limitations & roadmap](#known-limitations--roadmap)

---

## Features

| | Guest | Registered user | Admin |
|---|:---:|:---:|:---:|
| Browse approved ads & view details | ✅ | ✅ | ✅ |
| Search ads by **brand** or **model** | ✅ | ✅ | ✅ |
| **Report a lost/stolen phone** (IMEI 1, IMEI 2, GD no.) | ✅ | ✅ | |
| Sign up / log in (with "remember me") | ✅ | ✅ | ✅ |
| **Post a sell ad** (details, IMEIs, prices, NID, memo, photos) | | ✅ | |
| **My Ads**: see your own ads and their status | | ✅ | |
| Edit or delete your own ad (edits go back to review) | | ✅ | |
| **Review Post**: filter ads by *Approved* / *Pending* | | | ✅ |
| View seller's NID scans | | | ✅ |
| **Check IMEI** against the stolen registry | | | ✅ |
| **Approve / Decline** an ad | | | ✅ |
| Exchange phones | 🚧 *coming soon* | 🚧 | 🚧 |

---

## See it in action

> These are animated SVGs. They play automatically on GitHub and in most Markdown viewers.

### 1. The stolen-IMEI check

The admin opens a pending ad and clicks **Check**. The ad's IMEI pair is looked up in the `Imei` table. A clean phone can be approved. A phone that matches a reported row is flagged with a **MATCHED** warning and should be declined.

<p align="center">
  <img src="docs/imei-check-simulation.svg" alt="Animated simulation of the IMEI check: one clean phone that gets approved and one stolen phone that gets rejected" width="100%">
</p>

### 2. The life of a "Sell" ad

<p align="center">
  <img src="docs/ad-lifecycle-simulation.svg" alt="Animated simulation of an ad moving from the sell form to pending review, admin review, and finally the public Buy page" width="100%">
</p>

---

## Screenshots

> **Note:** these are faithful *reconstructions*, not captures of a live deployment. Each page was rebuilt as static HTML from the project's own `.aspx` markup, Bootstrap 5.2 build and stylesheets (`StyleSheet1.css`, `reviewpost.css`, `detailspost.css`, …), filled with sample data, and captured in headless Chrome at 1440px width. NID scans are shown as placeholders.

### Home

<p align="center"><img src="docs/screenshots/home.png" alt="Home page with carousel, Buy/Sell/Exchange cards and IMEI info section" width="85%"></p>

<table>
  <tr>
    <td width="50%"><b>Report a lost phone</b> (navbar → Report Lost Imei)<br><img src="docs/screenshots/report-imei.png" alt="Report lost IMEI modal"></td>
    <td width="50%"><b>User login</b><br><img src="docs/screenshots/login.png" alt="User login modal"></td>
  </tr>
</table>

### Buying

<table>
  <tr>
    <td width="50%"><b>Buy</b>: approved ads with brand/model search<br><img src="docs/screenshots/buy.png" alt="Buy page grid of approved phone ads"></td>
    <td width="50%"><b>Ad details</b> (public view)<br><img src="docs/screenshots/details-public.png" alt="Ad details page as seen by a buyer"></td>
  </tr>
</table>

### Selling

<table>
  <tr>
    <td width="50%"><b>Sell form</b><br><img src="docs/screenshots/sell.png" alt="Sell ad form with phone details and uploads"></td>
    <td width="50%"><b>Sign up</b><br><img src="docs/screenshots/signup.png" alt="Sign up form"></td>
  </tr>
  <tr>
    <td width="50%"><b>My Ads</b>: the seller's ads and their status<br><img src="docs/screenshots/my-ads.png" alt="My Ads page with Approved, Pending and Rejected ads"></td>
    <td width="50%"><b>Owner view</b>: Update / Delete own ad<br><img src="docs/screenshots/details-owner.png" alt="Ad details page with Update and Delete buttons"></td>
  </tr>
</table>

### Admin

<table>
  <tr>
    <td width="50%"><b>Review Post</b>: filter Approved / Pending<br><img src="docs/screenshots/review-post.png" alt="Admin review page listing ads with status"></td>
    <td width="50%"><b>Ad details (admin)</b>: IMEIs, NID, Approve / Decline / Check, after a <b>MATCHED</b> result<br><img src="docs/screenshots/details-admin-matched.png" alt="Admin ad details page showing the MATCHED stolen-phone warning"></td>
  </tr>
</table>

---

## How it works

### Architecture

```mermaid
flowchart LR
    subgraph Browser
        G[Guest]
        U[User]
        A[Admin]
    end

    subgraph IIS["ASP.NET Web Forms app (IIS Express)"]
        M["Site1.Master<br/>navbar · login modals · report-IMEI modal"]
        P1["WebForm1.aspx<br/>Home"]
        P2["Buy.aspx / WebForm4.aspx<br/>Browse & search"]
        P3["WebForm2.aspx<br/>Sell form"]
        P4["WebForm3.aspx<br/>Ad details · admin actions"]
        P5["reviewpost.aspx<br/>Admin review"]
        P6["mypost.aspx<br/>My Ads"]
        P7["usersignup.aspx<br/>Sign up"]
    end

    subgraph Storage
        DB[("SQL Server LocalDB<br/>App_Data/Database1.mdf")]
        FS[("image/ folder<br/>uploaded photos, NID, memo")]
    end

    G & U & A --> M
    M --> P1 & P2 & P3 & P4 & P5 & P6 & P7
    P2 & P3 & P4 & P5 & P6 & P7 & M --> DB
    P3 --> FS
```

### Reporting a lost phone and catching it later

```mermaid
sequenceDiagram
    autonumber
    actor Owner as Phone owner
    actor Seller
    actor Admin
    participant App as Smart & Safe
    participant DB as Database

    Owner->>App: "Report Lost IMEI" (IMEI 1, IMEI 2, GD no.)
    App->>DB: INSERT INTO Imei
    Note over DB: Phone is now in the stolen registry

    Seller->>App: Submit sell ad (IMEIs, NID, memo, photos)
    App->>DB: INSERT INTO sell_post (status = 'Pending')
    App-->>Seller: "Your post will be reviewed by admin before publish"

    Admin->>App: Review Post → open ad → Check
    App->>DB: SELECT * FROM Imei WHERE imei1 = ? AND imei2 = ?
    alt match found
        DB-->>App: row
        App-->>Admin: ⚠ "Match Found!" + MATCHED badge
        Admin->>App: Decline
        App->>DB: UPDATE sell_post SET status = 'Rejected'
    else no match
        DB-->>App: empty
        App-->>Admin: "No match found"
        Admin->>App: Approve
        App->>DB: UPDATE sell_post SET status = 'Approved'
        Note over App: Ad now visible on Buy page
    end
```

### Ad status

```mermaid
stateDiagram-v2
    [*] --> Pending: seller submits ad
    Pending --> Approved: admin approves
    Pending --> Rejected: admin declines (e.g. IMEI matched)
    Approved --> Pending: seller edits ad
    Rejected --> Pending: seller edits ad
    Pending --> [*]: seller deletes
    Approved --> [*]: seller deletes
    Rejected --> [*]: seller deletes
```

Only **Approved** ads are shown on the Buy page and in search results.

---

## Pages

| Page | Route | Who | What it does |
|---|---|---|---|
| Master layout | `Site1.Master` | everyone | Navbar, user/admin login modals, **Report Lost IMEI** modal, footer |
| Home | `WebForm1.aspx` | everyone | Carousel, Buy / Sell / Exchange cards, "why check IMEI" info |
| Buy | `Buy.aspx` | everyone | Approved ads with brand/model search |
| Listings | `WebForm4.aspx` | everyone | Grid of all approved ads |
| Ad details | `WebForm3.aspx?id=…` | everyone / owner / admin | Full ad info. **Owner:** Update, Delete. **Admin:** NID view, Approve, Decline, Check |
| Sell | `WebForm2.aspx` | logged-in user | Sell form with file uploads |
| My Ads | `mypost.aspx` | logged-in user | The user's own ads |
| Review Post | `reviewpost.aspx` | admin | Filter ads by Approved / Pending |
| Sign up | `usersignup.aspx` | guest | Create an account (username, full name, NID, phone, email, DOB) |
| Exchange | `WebForm10.aspx` | everyone | 🚧 Under construction |

---

## Data model

These tables and columns are taken from the SQL queries in the code.

```mermaid
erDiagram
    user_login ||--o{ sell_post : "posts (username)"
    sell_post }o..o| Imei : "checked against (imei1, imei2)"

    user_login {
        string username PK
        string fullname
        string nid
        string contact_no
        string email
        string password
        date   date_of_birth
    }

    admin_login {
        string id PK
        string name
        string password
    }

    sell_post {
        int    id PK
        string username FK
        string fname
        string lname
        string contact_no
        string brand
        string model
        string imei1
        string imei2
        string buying_year
        string buying_price
        string asking_price
        string description
        string thumbnail
        string pic1
        string pic2
        string nid1
        string nid2
        string memo
        string status "Pending, Approved or Rejected"
    }

    Imei {
        string imei1
        string imei2
        string gd_no "police General Diary no."
    }
```

Uploaded files (thumbnail, photos, NID front/back, purchase memo) are saved to `/image/`. Their relative paths are stored in `sell_post`.

---

## Getting started

### Prerequisites

- **Windows**
- **Visual Studio 2019 or 2022** with the *ASP.NET and web development* workload
- **.NET Framework 4.7.2** targeting pack
- **SQL Server Express LocalDB** (installed with Visual Studio's web workload)

### Run it

1. Clone or download the repository.
2. In Visual Studio, choose **File → Open → Project/Solution** and open `Smart and safe.csproj`. Visual Studio creates a solution for you.
3. Restore NuGet packages. The only one is `Microsoft.CodeDom.Providers.DotNetCompilerPlatform`.
4. Press **F5**. The site starts on IIS Express and opens the home page.

The database is `App_Data/Database1.mdf`. It attaches automatically through this connection string in `Web.config`:

```xml
Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Database1.mdf;Integrated Security=True
```

### Create an admin account

Admins can't sign up through the site. Add one directly to the database: open **Server Explorer → Database1.mdf → Tables → admin_login → Show Table Data** and insert a row with an `id`, a display name and a `password`. Then use the **Admin Login** link in the footer.

### Try the full flow

1. **Sign up** as a user and log in.
2. **Report Lost IMEI** from the navbar with an IMEI pair and a GD number.
3. Go to **Sell** and post an ad that uses the same IMEI pair.
4. Log out, then log in as **admin** → **Review Post** → tick *Pending* → **Go**.
5. Open the ad and click **Check**. You should see **Match Found!** and the MATCHED badge.
6. **Decline** it. Post another ad with clean IMEIs, **Approve** it, and check that it appears on **Buy**.

---

## Project structure

```
Smart-and-Safe/
├── App_Data/
│   └── Database1.mdf          # SQL Server LocalDB database
├── bootstrap/                 # Bootstrap 5.2 (css + js bundle)
├── Font/                      # Font Awesome 6.1 (local copy)
├── image/                     # Site images + uploaded ad files
├── docs/                      # README visuals, animated simulations, screenshots
│
├── Site1.Master(.cs)          # Layout, auth (user/admin login), report-IMEI
├── WebForm1.aspx(.cs)         # Home
├── Buy.aspx(.cs)              # Browse + search approved ads
├── WebForm4.aspx(.cs)         # Approved listings grid
├── WebForm2.aspx(.cs)         # Sell form
├── WebForm3.aspx(.cs)         # Ad details, owner edit/delete, admin approve/decline/check
├── mypost.aspx(.cs)           # My Ads
├── reviewpost.aspx(.cs)       # Admin review dashboard
├── usersignup.aspx(.cs)       # Registration
├── WebForm10.aspx(.cs)        # Exchange (under construction)
│
├── StyleSheet1.css · SellStyle.css · detailspost.css · reviewpost.css
├── Web.config                 # Connection string, target framework
└── Smart and safe.csproj
```

---

## Known limitations & roadmap

This is a learning/academic project. Fix the points below before deploying it anywhere public.

**Security**
- [ ] **SQL injection:** login, signup's username check, Buy search, and the `?id=` queries in `WebForm3.aspx.cs` build SQL by string concatenation. Move them all to parameterised queries, as the INSERTs already do.
- [ ] **Passwords:** they're stored in plain text in the database and, with "remember me", in plain-text cookies. Hash them (e.g. PBKDF2/bcrypt) and use an auth token instead.
- [ ] **Authorization:** admin actions are hidden in the UI but not checked on the server.
- [ ] **Uploads:** files are saved under their original names in `/image/`, so names can collide and file types aren't validated. Generate unique names and restrict extensions.

**Bugs & features**
- [ ] The purchase memo upload saves the wrong file (`WebForm2.aspx.cs:112` calls `FileUpload.PostedFile.SaveAs` instead of `FileUpload3`).
- [ ] **Exchange** page (`WebForm10.aspx`) is a placeholder.
- [ ] **My Profile** and **Forgot Password** links aren't wired up yet.
- [ ] Let buyers run a public **"Verify IMEI"** check themselves.
- [ ] Give pages meaningful names (`WebForm2` → `Sell`, `WebForm3` → `AdDetails`, …).

---

<p align="center"><sub>Built with ASP.NET Web Forms · Buy smart, sell safe.</sub></p>
