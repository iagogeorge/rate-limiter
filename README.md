# rate-limiter
Rate Limiter in Go  A rate limiter implemented in Go for controlling the rate of requests to a web service. This project allows you to limit requests based on IP addresses or access tokens, providing a flexible and scalable solution for managing traffic.
### README.md

# Rate Limiter in Go

## Objective

The objective of this project is to develop a rate limiter in Go that can be configured to limit the maximum number of requests per second based on either a specific IP address or an access token.

## Description

This rate limiter is designed to control the traffic of requests to a web service. It is capable of limiting the number of requests based on two criteria:

- **IP Address**: The rate limiter restricts the number of requests received from a single IP address within a defined time interval.
- **Access Token**: The rate limiter can also limit requests based on a unique access token, allowing different expiration time limits for different tokens. The token must be provided in the header in the following format: `API_KEY: <TOKEN>`. The token's rate limit configuration should override the IP's limit. For example, if the limit per IP is 10 requests per second and a specific token has a limit of 100 requests per second, the rate limiter should use the token's information.

## Requirements

- The rate limiter should work as middleware injected into the web server.
- It should allow the configuration of the maximum number of requests permitted per second.
- It should offer the option to choose the blocking time for the IP or Token if the request limit has been exceeded.
- Configuration of limits should be done via environment variables or a `.env` file in the root folder.
- It should be possible to configure the rate limiter for both IP and access token limitation.
- The system should respond appropriately when the limit is exceeded:
  - **HTTP Code**: 429
  - **Message**: "You have reached the maximum number of requests or actions allowed within a certain time frame"
- All rate limiting information should be stored and retrieved from a Redis database. Docker Compose can be used to set up Redis.
- Create a strategy that allows easy switching from Redis to another persistence mechanism.
- The logic of the limiter should be separated from the middleware.

## Examples

### Limitation by IP

Suppose the rate limiter is configured to allow a maximum of 5 requests per second per IP. If the IP `192.168.1.1` sends 6 requests in one second, the sixth request should be blocked.

### Limitation by Token

If a token `abc123` is configured with a limit of 10 requests per second and sends 11 requests in that interval, the eleventh request should be blocked.

In both cases, subsequent requests can only be made once the total expiration time has elapsed. For example, if the expiration time is 5 minutes, the IP can make new requests only after 5 minutes.

## Tips

Test your rate limiter under different load conditions to ensure it works as expected in high traffic situations.

## Delivery

- The complete source code of the implementation.
- Documentation explaining how the rate limiter works and how it can be configured.
- Automated tests demonstrating the effectiveness and robustness of the rate limiter.
- Use Docker/Docker Compose so we can test your application.
- The web server should respond on port 8080.

## Setup

### Environment Variables

Configure the following environment variables in a `.env` file or directly in your environment:

- `REDIS_ADDR`: Address of the Redis server.
- `REDIS_PASSWORD`: Password for Redis (if any).
- `PERSISTENCE_BACKEND`: The persistence backend to use (`redis`).
- `DEFAULT_LIMIT`: Default rate limit in requests per second.
- `RATE_LIMITER_IP_MAX_REQUESTS`: Maximum requests per second per IP.
- `RATE_LIMITER_TOKEN_MAX_REQUESTS`: Maximum requests per second per token.
- `RATE_LIMITER_TIME_WINDOW_MILLISECONDS`: Time window for rate limiting in milliseconds.

### Docker Setup

1. **Build and Run**: Use Docker Compose to build and run the application and Redis.

   ```bash
   docker-compose up --build
   ```

2. **Access**: The application will be available at `http://localhost:8080`.

### Testing

Send requests to `http://localhost:8080/request` with or without an `API_KEY` header to test the rate limiter functionality.

## License

This project is licensed under the MIT License.
