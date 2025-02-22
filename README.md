# High School Film Club

Welcome to the High School Film Club project! This system includes portals for admins, members, and staff, as well as a section for managing kids being taken care of by volunteer workers. The system uses GitHub OAuth for Single Sign-On (SSO).

## Table of Contents

- [Setup](#setup)
- [GitHub OAuth App Configuration](#github-oauth-app-configuration)
- [Running the Application](#running-the-application)
- [Project Structure](#project-structure)
- [Environment Variables](#environment-variables)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Setup

### 1. Clone the Repository

Clone this repository to your local machine using GitHub Desktop or another method.

### 2. Install Dependencies

Go to the `film-club` directory and install the necessary dependencies. To do this, navigate to the repository on GitHub, click on the "Code" button, and follow the instructions to clone the repository.

### 3. Create Required Files and Directories

Create the following directories and files in your project:
### 4. Add Content to Files

#### `app.js`

```javascript
const express = require('express');
const session = require('express-session');
const passport = require('passport');
const GitHubStrategy = require('passport-github2').Strategy;
const path = require('path');
const app = express();
const PORT = 3000;

const GITHUB_CLIENT_ID = 'YOUR_GITHUB_CLIENT_ID';
const GITHUB_CLIENT_SECRET = 'YOUR_GITHUB_CLIENT_SECRET';

// Passport session setup.
passport.serializeUser((user, done) => {
  done(null, user);
});

passport.deserializeUser((obj, done) => {
  done(null, obj);
});

// Use the GitHubStrategy within Passport.
passport.use(new GitHubStrategy({
    clientID: GITHUB_CLIENT_ID,
    clientSecret: GITHUB_CLIENT_SECRET,
    callbackURL: 'http://localhost:3000/auth/github/callback'
  },
  function(accessToken, refreshToken, profile, done) {
    return done(null, profile);
  }
));

app.use(express.static(path.join(__dirname, 'public')));
app.set('views', path.join(__dirname, 'views'));
app.engine('html', require('ejs').renderFile);

// Configure session management.
app.use(session({
  secret: 'YOUR_SESSION_SECRET',
  resave: false,
  saveUninitialized: true
}));

// Initialize Passport and restore authentication state, if any, from the session.
app.use(passport.initialize());
app.use(passport.session());

app.get('/auth/github',
  passport.authenticate('github', { scope: ['user:email'] }));

app.get('/auth/github/callback', 
  passport.authenticate('github', { failureRedirect: '/' }),
  (req, res) => {
    res.redirect('/profile');
  });

app.get('/logout', (req, res) => {
  req.logout();
  res.redirect('/');
});

app.get('/profile', (req, res) => {
  if (!req.isAuthenticated()) {
    return res.redirect('/');
  }
  res.render('profile.html', { user: req.user });
});

app.get('/', (req, res) => {
  res.render('index.html');
});

app.get('/admin', (req, res) => {
  res.render('admin.html');
});

app.get('/member', (req, res) => {
  res.render('member.html');
});

app.get('/staff', (req, res) => {
  res.render('staff.html');
});

app.get('/manage-kids', (req, res) => {
  res.render('manage-kids.html');
});

app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>High School Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Welcome to the High School Film Club</h1>
    </header>
    <main>
        <section>
            <h2>Login</h2>
            <a href="/auth/github">Login with GitHub</a>
        </section>
    </main>
    <script src="/js/script.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Profile</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Welcome, <%= user.username %></h1>
    </header>
    <main>
        <section>
            <h2>Your Profile</h2>
            <p>Name: <%= user.displayName || user.username %></p>
            <p>Email: <%= user.emails[0].value %></p>
            <a href="/logout">Logout</a>
        </section>
    </main>
    <script src="/js/script.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Profile</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Welcome, <%= user.username %></h1>
    </header>
    <main>
        <section>
            <h2>Your Profile</h2>
            <p>Name: <%= user.displayName || user.username %></p>
            <p>Email: <%= user.emails[0].value %></p>
            <a href="/logout">Logout</a>
        </section>
    </main>
    <script src="/js/script.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Portal - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Admin Portal</h1>
    </header>
    <main>
        <section>
            <h2>Manage Club Members</h2>
            <!-- Admin functionalities go here -->
        </section>
    </main>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Member Portal - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Member Portal</h1>
    </header>
    <main>
        <section>
            <h2>Your Profile</h2>
            <!-- Member functionalities go here -->
        </section>
    </main>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Staff Portal - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Staff Portal</h1>
    </header>
    <main>
        <section>
            <h2>Manage Volunteers</h2>
            <!-- Staff functionalities go here -->
        </section>
    </main>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Manage Kids - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Manage Kids</h1>
    </header>
    <main>
        <section>
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f0f0f0;
}

header {
    background-color: #333;
    color: #fff;
    padding: 1rem;
    text-align: center;
}

main {
    padding: 2rem;
}

form {
    display: flex;
    flex-direction: column;
    max-width: 400px;
    margin: 0 auto;
}

label {
    margin-bottom: 0.5rem;
}

input, select, button {
    margin-bottom: 1rem;
    padding: 0.5rem;
}
document.getElementById('loginForm').addEventListener('submit', function(event) {
    event.preventDefault();
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;
    const role = document.getElementById('role').value;

    // Temporary login logic for demonstration
    if (username === 'admin' && password === 'admin' && role === 'admin') {
        window.location.href = '/admin';
    } else if (username === 'member' && password === 'member' && role === 'member') {
        window.location.href = '/member';
    } else if (username === 'staff' && password === 'staff' && role === 'staff') {
        window.location.href = '/staff';
    } else {
        alert('Invalid credentials or role');
    }
});
document.getElementById('loginForm').addEventListener('submit', function(event) {
    event.preventDefault();
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;
    const role = document.getElementById('role').value;

    // Temporary login logic for demonstration
    if (username === 'admin' && password === 'admin' && role === 'admin') {
        window.location.href = '/admin';
    } else if (username === 'member' && password === 'member' && role === 'member') {
        window.location.href = '/member';
    } else if (username === 'staff' && password === 'staff' && role === 'staff') {
        window.location.href = '/staff';
    } else {
        alert('Invalid credentials or role');
    }
});
            <h2>Kids Being Cared For</h2>
            <!-- Manage kids functionalities go here -->
        </section>
    </main>
</body>
</html>
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret
SESSION_SECRET=your_session_secret
# High School Film Club

Welcome to the High School Film Club project! This system includes portals for admins, members, and staff, as well as a section for managing kids being taken care of by volunteer workers. The system uses GitHub OAuth for Single Sign-On (SSO).

## Table of Contents

- [Setup](#setup)
- [GitHub OAuth App Configuration](#github-oauth-app-configuration)
- [Running the Application](#running-the-application)
- [Project Structure](#project-structure)
- [Environment Variables](#environment-variables)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Setup

### 1. Clone the Repository

Clone this repository to your local machine using GitHub Desktop or another method.

### 2. Install Dependencies

Go to the `film-club` directory and install the necessary dependencies. To do this, navigate to the repository on GitHub, click on the "Code" button, and follow the instructions to clone the repository.

### 3. Create Required Files and Directories

Create the following directories and files in your project:
# High School Film Club

Welcome to the High School Film Club project! This system includes portals for admins, members, and staff, as well as a section for managing kids being taken care of by volunteer workers. The system uses GitHub OAuth for Single Sign-On (SSO).

## Table of Contents

- [Setup](#setup)
- [GitHub OAuth App Configuration](#github-oauth-app-configuration)
- [Running the Application](#running-the-application)
- [Project Structure](#project-structure)
- [Environment Variables](#environment-variables)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Setup

### 1. Clone the Repository

Clone this repository to your local machine using GitHub Desktop or another method.

### 2. Install Dependencies

Go to the `film-club` directory and install the necessary dependencies. To do this, navigate to the repository on GitHub, click on the "Code" button, and follow the instructions to clone the repository.

### 3. Create Required Files and Directories

### 4. Add Content to Files

#### `app.js`

```javascript
const express = require('express');
const session = require('express-session');
const passport = require('passport');
const GitHubStrategy = require('passport-github2').Strategy;
const path = require('path');
const app = express();
const PORT = 3000;

const GITHUB_CLIENT_ID = 'YOUR_GITHUB_CLIENT_ID';
const GITHUB_CLIENT_SECRET = 'YOUR_GITHUB_CLIENT_SECRET';

// Passport session setup.
passport.serializeUser((user, done) => {
  done(null, user);
});

passport.deserializeUser((obj, done) => {
  done(null, obj);
});

// Use the GitHubStrategy within Passport.
passport.use(new GitHubStrategy({
    clientID: GITHUB_CLIENT_ID,
    clientSecret: GITHUB_CLIENT_SECRET,
    callbackURL: 'http://localhost:3000/auth/github/callback'
  },
  function(accessToken, refreshToken, profile, done) {
    return done(null, profile);
  }
));

app.use(express.static(path.join(__dirname, 'public')));
app.set('views', path.join(__dirname, 'views'));
app.engine('html', require('ejs').renderFile);

// Configure session management.
app.use(session({
  secret: 'YOUR_SESSION_SECRET',
  resave: false,
  saveUninitialized: true
}));

// Initialize Passport and restore authentication state, if any, from the session.
app.use(passport.initialize());
app.use(passport.session());

app.get('/auth/github',
  passport.authenticate('github', { scope: ['user:email'] }));

app.get('/auth/github/callback', 
  passport.authenticate('github', { failureRedirect: '/' }),
  (req, res) => {
    res.redirect('/profile');
  });

app.get('/logout', (req, res) => {
  req.logout();
  res.redirect('/');
});

app.get('/profile', (req, res) => {
  if (!req.isAuthenticated()) {
    return res.redirect('/');
  }
  res.render('profile.html', { user: req.user });
});

app.get('/', (req, res) => {
  res.render('index.html');
});

app.get('/admin', (req, res) => {
  res.render('admin.html');
});

app.get('/member', (req, res) => {
  res.render('member.html');
});

app.get('/staff', (req, res) => {
  res.render('staff.html');
});

app.get('/manage-kids', (req, res) => {
  res.render('manage-kids.html');
});

app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>High School Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Welcome to the High School Film Club</h1>
    </header>
    <main>
        <section>
            <h2>Login</h2>
            <a href="/auth/github">Login with GitHub</a>
        </section>
    </main>
    <script src="/js/script.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Profile</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Welcome, <%= user.username %></h1>
    </header>
    <main>
        <section>
            <h2>Your Profile</h2>
            <p>Name: <%= user.displayName || user.username %></p>
            <p>Email: <%= user.emails[0].value %></p>
            <a href="/logout">Logout</a>
        </section>
    </main>
    <script src="/js/script.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Portal - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Admin Portal</h1>
    </header>
    <main>
        <section>
            <h2>Manage Club Members</h2>
            <!-- Admin functionalities go here -->
        </section>
    </main>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Member Portal - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Member Portal</h1>
    </header>
    <main>
        <section>
            <h2>Your Profile</h2>
            <!-- Member functionalities go here -->
        </section>
    </main>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Staff Portal - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Staff Portal</h1>
    </header>
    <main>
        <section>
            <h2>Manage Volunteers</h2>
            <!-- Staff functionalities go here -->
        </section>
    </main>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Manage Kids - Film Club</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <header>
        <h1>Manage Kids</h1>
    </header>
    <main>
        <section>
            <h2>Kids Being Cared For</h2>
            <!-- Manage kids functionalities go here -->
        </section>
    </main>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f0f0f0;
}

header {
    background-color: #333;
    color: #fff;
    padding: 1rem;
    text-align: center;
}

main {
    padding: 2rem;
}

form {
    display: flex;
    flex-direction: column;
    max-width: 400px;
    margin: 0 auto;
}

label {
    margin-bottom: 0.5rem;
}

input, select, button {
    margin-bottom: 1rem;
    padding: 0.5rem;
}
document.getElementById('loginForm').addEventListener('submit', function(event) {
    event.preventDefault();
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;
    const role = document.getElementById('role').value;

    // Temporary login logic for demonstration
    idocument.getElementById('loginForm').addEventListener('submit', function(event) {
    event.preventDefault();
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;
    const role = document.getElementById('role').value;

    // Temporary login logic for demonstration
    if (username === 'admin' && password === 'admin' && role === 'admin') {
        window.location.href = '/admin';
    } else if (username === 'member' && password === 'member' && role === 'member') {
        window.location.href = '/member';
    } else if (username === 'staff' && password === 'staff' && role === 'staff') {
        window.location.href = '/staff';
    } else {
        alert('Invalid credentials or role');
    }
});f (username === 'admin' && password === 'admin' && role === 'admin') {
        window.location.href = '/admin';
    } else if (username === 'member' && password === 'member' && role === 'member') {
        window.location.href = '/member';
    } else if (username === 'staff' && password === 'staff' && role === 'staff') {
        window.location.href = '/staff';
    } else {
        alert('Invalid credentials or role');
    }
});

document.getElementById('loginForm').addEventListener('submit', function(event) {
    event.preventDefault();
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;
    const role = document.getElementById('role').value;

    // Temporary login logic for demonstration
    if (username === 'admin' && password === 'admin' && role === 'admin') {
        window.location.href = '/admin';
    } else if (username === 'member' && password === 'member' && role === 'member') {
        window.location.href = '/member';
    } else if (username === 'staff' && password === 'staff' && role === 'staff') {
        window.location.href = '/staff';
    } else {
        alert('Invalid credentials or role');
    }
});
