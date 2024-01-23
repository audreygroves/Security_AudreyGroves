# JWT-secured Ads API

## Overview

This README provides an overview of a Node.js Express application that serves as an Ads API, with a focus on enhancing security using JSON Web Tokens (JWT). The API allows users to interact with a collection of ads, including inserting, updating, and deleting ads.

## Dependencies

- Express: A fast, unopinionated, minimalist web framework for Node.js.
- Body-parser: Middleware to parse incoming JSON requests.
- Cors: Middleware to enable Cross-Origin Resource Sharing (CORS).
- Helmet: Middleware to enhance API security by setting various HTTP headers.
- Morgan: HTTP request logger middleware for Node.js.
- MongoDB (In-memory): Temporary in-memory database solution.
- JSON Web Token (JWT): A standard for secure transmission of information between parties.

## Usage of JWT for Security

The application employs JWT to secure its endpoints and validate the authenticity of incoming requests.

### Key Components

#### 1. Initialization

The JWT-related dependencies are imported at the beginning of the file:

```javascript
const jwt = require('express-jwt');
const jwksRsa = require('jwks-rsa');
```

#### 2. JWT Middleware

A middleware function named `checkJwt` is defined using `express-jwt`. This middleware validates the JWT present in the request header.

```javascript
const checkJwt = jwt.expressjwt({
    secret: jwksRsa.expressJwtSecret({
      cache: true,
      rateLimit: true,
      jwksRequestsPerMinute: 5,
      jwksUri: 'https://dev-8ejo2jpb38zxjzmb.auth0.com/.well-known/jwks.json'
    }),
    audience: 'https://ads-api',
    issuer: 'https://dev-8ejo2jpb38zxjzmb.auth0.com/',
    algorithms: ['RS256']
});
```

This middleware is applied globally to secure all routes below it:

```javascript
app.use(checkJwt);
```

### Endpoints

#### 1. Retrieve Ads

The application includes an endpoint to retrieve all ads. The JWT validation ensures only authenticated users can access this endpoint.

```javascript
app.get('/', async (req, res) => {
    res.send(await getAds());
});
```

#### 2. Insert Ad

Authenticated users can insert a new ad by sending a POST request to the `/` endpoint.

```javascript
app.post('/', async (req, res) => {
    const newAd = req.body;
    await insertAd(newAd);
    res.send({ message: 'New ad inserted.' });
});
```

#### 3. Delete Ad

Deleting an ad is restricted to authenticated users by validating the JWT.

```javascript
app.delete('/:id', async (req, res) => {
    await deleteAd(req.params.id);
    res.send({ message: 'Ad removed.' });
});
```

#### 4. Update Ad

Similarly, updating an ad requires authentication via JWT validation.

```javascript
app.put('/:id', async (req, res) => {
    const updatedAd = req.body;
    await updateAd(req.params.id, updatedAd);
    res.send({ message: 'Ad updated.' });
});
```

## Running the Application

1. Ensure MongoDB is installed and running.
2. Install dependencies using `npm install`.
3. Start the in-memory MongoDB instance by running `startDatabase()`.
4. Insert an initial ad into the database.
5. Start the server using `npm start` or `node ./src/index.js`.

The server will be listening on port 3001.

**Note:** Ensure you have the necessary environment variables for Auth0 (Issuer URI, Audience, etc.) set before running the application.
