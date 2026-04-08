
# Google OAuth 2.0 with Express & Passport

A lightweight Node.js/Express application demonstrating how to implement Google OAuth 2.0 authentication using `passport` and `passport-google-oauth20`.

## 🚀 Prerequisites

Before running this project, you need to set up OAuth credentials in the Google Cloud Console:
1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project.
3. Navigate to **APIs & Services > Credentials**.
4. Create **OAuth client ID** credentials (Application type: Web application).
5. Set the Authorized redirect URIs (e.g., `http://localhost:3000/auth/google/callback`).

## 🛠️ Environment Variables

Create a `.env` file in the root of your project and add the credentials you obtained from Google:

```env
CLIENT_ID=your_google_client_id_here
CLIENT_SECRET=your_google_client_secret_here
URL=http://localhost:3000/auth/google/callback
```

## 📦 Installation

Install the required dependencies:

```bash
npm install express passport passport-google-oauth20 dotenv morgan
```

## 💻 Usage

Run your server file (e.g., `node server.js`):

```javascript
require('dotenv').config();
const express = require("express");
const passport = require("passport");
const GoogleStrategy = require("passport-google-oauth20").Strategy;
const morgan = require("morgan");

const app = express();

app.get("/", (req, res) => {
    res.send("You cancelled the Google auth or are on the home page.");
});

// Mandatory setup for Passport x Google Auth
app.use(passport.initialize());

passport.use(new GoogleStrategy({
    clientID: process.env.CLIENT_ID,
    clientSecret: process.env.CLIENT_SECRET,
    callbackURL: process.env.URL
}, (accessToken, refreshToken, profile, done) => {
    // This runs after Google verifies the user
    return done(null, profile);
}));

// Route to initiate Google Auth
app.get("/auth/google",
    passport.authenticate("google", {
        session: false,
        scope: ["profile", "email"] // Requests user's profile and email
    })
);

// Route for Google Auth Callback
app.get("/auth/google/callback",
    passport.authenticate("google", {
        session: false,
        failureRedirect: "/" // Redirect here if the user cancels
    }), (req, res) => {
        // If successful, req.user contains the Google profile data
        console.log(req.user);
        res.send("You are logged in!");
    }
);

app.listen(3000, () => {
    console.log("Server is running on port 3000");
});
```

---

## 📖 How It Works

1. **The Strategy Setup:** `passport.use(new GoogleStrategy(...))` configures your app with Google's API. The callback function `(accessToken, refreshToken, profile, done)` is triggered once Google successfully authenticates the user, handing over their `profile` data.
2. **The Trigger (`/auth/google`):** When a user hits this route, Passport redirects them to Google's official login/consent screen. The `scope` array dictates what data you are asking permission to access.
3. **The Handshake (`/auth/google/callback`):** After the user clicks "Allow", Google redirects them here with an authorization code. Passport validates this code and triggers your final route handler, attaching the user's data to `req.user`.

---

## 🔐 `session: true` vs. `session: false`

In the code above, `session: false` is used. Understanding when to use sessions depends entirely on your application's architecture.

### When to use `session: false` (Stateless)
Use this when building a **REST API** that serves a Single Page Application (React, Vue, Angular) or a Mobile App.

* **How it works:** The server does not remember the user after the initial login. Instead, inside your callback route, you generate a token (like a JWT - JSON Web Token), send it to the frontend, and the frontend sends that token back with every subsequent request.
* **Pros:** Highly scalable, works easily across different domains (CORS), and perfect for decoupled frontend/backend setups.
* **Implementation:** As seen in your code. You handle the token generation inside the successful callback route.

### When to use `session: true` (Stateful)
Use this when building a **Server-Rendered Application** (using template engines like EJS, Pug, or Handlebars) where the frontend and backend are tightly coupled.

* **How it works:** The server creates a unique session ID for the user, stores it in a database/memory, and sends a cookie to the user's browser. The browser automatically sends this cookie back on future requests, keeping the user logged in.
* **Pros:** Simpler to implement for traditional web apps. No need to manually manage tokens on the client side.
* **Implementation requirements:** If you change to `session: true`, you **must** add two additional Passport methods to tell it how to store and retrieve the user from the session:

  ```javascript
  // Required middleware for sessions
  const session = require('express-session');
  app.use(session({ secret: 'some_secret_key', resave: false, saveUninitialized: true }));
  app.use(passport.session());

  // Tells Passport how to save the user into the session cookie
  passport.serializeUser((user, done) => {
      done(null, user.id); // Usually you just save the user ID
  });

  // Tells Passport how to retrieve the user from the database using the ID in the cookie
  passport.deserializeUser((id, done) => {
      // e.g., User.findById(id).then(user => done(null, user));
      done(null, { id: id, name: "Mock User" }); 
  });
  ```
