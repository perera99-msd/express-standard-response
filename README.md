# Express Standard Response

[![npm version](https://img.shields.io/npm/v/express-standard-response.svg)](https://www.npmjs.com/package/express-standard-response)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/node-%3E%3D16.0.0-339933.svg)](https://nodejs.org/)

A lightweight, zero-dependency utility to standardize API success and error responses in Express.js applications.

This package helps teams keep response payloads predictable across controllers, services, and middleware.

## Features

- Simple and consistent response format
- Dedicated classes for success and error flows
- Compatible with Express 4 and 5
- No runtime dependencies
- Works out of the box with CommonJS

## Installation

```bash
npm install express-standard-response
```

## Quick Start

```js
const express = require('express');
const { ApiResponse, ApiError } = require('express-standard-response');

const app = express();

app.get('/api/users', (req, res) => {
	const users = [{ id: 1, name: 'John Doe' }];
	return res
		.status(200)
		.json(new ApiResponse(200, users, 'Users fetched successfully'));
});

app.get('/api/protected', (req, res, next) => {
	const isAuthorized = false;
	if (!isAuthorized) {
		return next(new ApiError(401, 'Unauthorized access'));
	}
	return res.status(200).json(new ApiResponse(200, { ok: true }));
});

app.use((err, req, res, next) => {
	if (err instanceof ApiError) {
		return res.status(err.statusCode).json(err);
	}

	return res
		.status(500)
		.json(new ApiError(500, 'Internal Server Error'));
});
```

## API Reference

### ApiResponse

Creates a standardized success response object.

```js
new ApiResponse(statusCode, data, message)
```

- `statusCode` (`number`): HTTP status code
- `data` (`any`): response payload
- `message` (`string`, optional): default is `Success`

Output shape:

```json
{
	"statusCode": 200,
	"data": {},
	"message": "Success",
	"success": true
}
```

### ApiError

Creates a standardized error object.

```js
new ApiError(statusCode, message, errors, stack)
```

- `statusCode` (`number`): HTTP status code
- `message` (`string`, optional): default is `Something went wrong`
- `errors` (`Array`, optional): default is `[]`
- `stack` (`string`, optional): custom stack trace

Output shape:

```json
{
	"statusCode": 400,
	"data": null,
	"message": "Validation failed",
	"success": false,
	"errors": []
}
```

## Best Practices

- Use `ApiResponse` for all successful JSON responses
- Throw or pass `ApiError` instances to centralized error middleware
- Avoid returning ad-hoc response shapes across routes

## Roadmap

- Add TypeScript definitions
- Add automated tests and CI workflow
- Add optional helper factory methods

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## Code of Conduct

This project follows [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

## Security

Please report vulnerabilities according to [SECURITY.md](SECURITY.md).

## License

Licensed under the MIT License. See [LICENSE](LICENSE).
