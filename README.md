# Dismental Backend (MERN)

The backend API for the Dismental application, built on the MERN stack. It is a
Node.js / Express REST service that handles user registration and authentication,
persisting users in MongoDB and issuing JSON Web Tokens (JWT) for authenticated
sessions.

## Tech Stack

- **Runtime:** Node.js
- **Framework:** [Express](https://expressjs.com/) 4
- **Database:** MongoDB via [Mongoose](https://mongoosejs.com/) 7
- **Authentication:** [jsonwebtoken](https://github.com/auth0/node-jsonwebtoken) (JWT)
- **Password hashing:** [bcrypt](https://github.com/kelektiv/node.bcrypt.js)
- **Validation:** [Joi](https://joi.dev/) and [joi-password-complexity](https://github.com/kamronbatman/joi-password-complexity)
- **Other:** [cors](https://github.com/expressjs/cors), [dotenv](https://github.com/motdotla/dotenv)
- **Dev:** [nodemon](https://github.com/remy/nodemon)

## Project Structure

```
.
├── .gitignore
├── .vscode/
│   └── launch.json
├── README.md
└── server/
    ├── .env.example      # template for required environment variables
    ├── index.js          # app entry point: middleware + route mounting
    ├── db.js             # MongoDB connection helper
    ├── package.json
    ├── models/
    │   └── user.js       # User Mongoose schema + Joi validation + auth token
    └── routes/
        ├── users.js      # user registration
        └── auth.js       # user login
```

## API Endpoints

The server mounts two routers. All request/response bodies are JSON.

### `POST /api/users`

Register a new user.

Request body:

| Field       | Type   | Notes                                   |
| ----------- | ------ | --------------------------------------- |
| `firstName` | string | required                                |
| `lastName`  | string | required                                |
| `email`     | string | required, must be a valid email         |
| `password`  | string | required, must meet complexity rules    |

Responses:

- `201 Created` — `{ "message": "User created successfully" }`
- `400 Bad Request` — validation error (returns the first validation message)
- `409 Conflict` — `{ "message": "User with given email already Exist!" }`
- `500 Internal Server Error`

### `POST /api/auth`

Log in an existing user and receive a JWT.

Request body:

| Field      | Type   | Notes                           |
| ---------- | ------ | ------------------------------- |
| `email`    | string | required, must be a valid email |
| `password` | string | required                        |

Responses:

- `200 OK` — `{ "data": "<jwt>", "message": "logged in successfully" }`
- `400 Bad Request` — validation error
- `401 Unauthorized` — `{ "message": "Invalid Email or Password" }`
- `500 Internal Server Error`

The returned JWT is signed with `JWTPRIVATEKEY`, contains the user's `_id`, and
expires after 7 days.

## Environment Variables

The server reads configuration from a `.env` file in the `server/` directory
(loaded via `dotenv`). Copy the template and fill in your own values:

```bash
cp server/.env.example server/.env
```

| Variable        | Required | Description                                                      |
| --------------- | -------- | ---------------------------------------------------------------- |
| `DB`            | yes      | MongoDB connection string (e.g. a MongoDB Atlas SRV URI)         |
| `JWTPRIVATEKEY` | yes      | Secret key used to sign JWTs                                     |
| `SALT`          | yes      | Number of bcrypt salt rounds (e.g. `10`)                        |
| `PORT`          | no       | Port the server listens on (defaults to `8080`)                 |

> **Never commit your real `.env` file.** It is ignored by `.gitignore`. Keep
> secrets out of version control and rotate any credential that has ever been
> committed.

## Getting Started

### Prerequisites

- Node.js and npm installed
- A MongoDB database (local or hosted, e.g. MongoDB Atlas)

### Installation

```bash
git clone https://github.com/JayeshSuryavanshi/Dismental-Backend-MERN.git
cd Dismental-Backend-MERN/server
npm install
```

### Configuration

Create your environment file from the example and fill in the values:

```bash
cp .env.example .env
```

### Running

From the `server/` directory:

```bash
npm start
```

This runs the app with `nodemon` (auto-restart on file changes). Once running,
you should see `Listening on port 8080...` (or your configured `PORT`) and
`Connected to database successfully`.

## License

ISC (as declared in `server/package.json`).
