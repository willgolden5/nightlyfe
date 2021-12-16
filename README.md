# Nightlyfe boilerplate

## Features

The following feature set is a minimal selection of typical Web API requirements:

- Configuration using [viper](https://github.com/spf13/viper)
- CLI features using [cobra](https://github.com/spf13/cobra)
- PostgreSQL support including migrations using [go-pg](https://github.com/go-pg/pg)
- Structured logging with [Logrus](https://github.com/sirupsen/logrus)
- Routing with [chi router](https://github.com/go-chi/chi) and middleware
- JWT Authentication using [lestrrat-go/jwx](https://github.com/lestrrat-go/jwx) with example passwordless email authentication
- Request data validation using [ozzo-validation](https://github.com/go-ozzo/ozzo-validation)
- HTML emails with [go-mail](https://github.com/go-mail/mail)

## Start Application

- Clone this repository
- Create a postgres database and set environment variables for your database accordingly if not using same as default
- Run the application to see available commands: `go run main.go`
- First initialize the database running all migrations found in ./database/migrate at once with command _migrate_: `go run main.go migrate`
- Run the application with command _serve_: `go run main.go serve`

Or just use the provided docker-compose file. After first start attach to the server container and run `./main migrate` to populate the database.

## API Routes

### Authentication

For passwordless login following routes are available:

| Path          | Method | Required JSON | Header                                | Description                                                             |
| ------------- | ------ | ------------- | ------------------------------------- | ----------------------------------------------------------------------- |
| /auth/login   | POST   | email         |                                       | the email you want to login with (see below)                            |
| /auth/token   | POST   | token         |                                       | the token you received via email (or printed to stdout if smtp not set) |
| /auth/refresh | POST   |               | Authorization: "Bearer refresh_token" | refresh JWTs                                                            |
| /auth/logout  | POST   |               | Authorizaiton: "Bearer refresh_token" | logout from this device                                                 |

### Environment Variables

By default viper will look at $HOME/.go-base.yaml for a config file. Setting your config as Environment Variables is recommended as by 12-Factor App.

| Name                    | Type          | Default                     | Description                                                                                                                                                                                               |
| ----------------------- | ------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PORT                    | string        | localhost:3000              | http address (accepts also port number only for heroku compability)                                                                                                                                       |
| LOG_LEVEL               | string        | debug                       | log level                                                                                                                                                                                                 |
| LOG_TEXTLOGGING         | bool          | false                       | defaults to json logging                                                                                                                                                                                  |
| DB_NETWORK              | string        | tcp                         | database 'tcp' or 'unix' connection                                                                                                                                                                       |
| DB_ADDR                 | string        | localhost:5432              | database tcp address or unix socket                                                                                                                                                                       |
| DB_USER                 | string        | postgres                    | database user name                                                                                                                                                                                        |
| DB_PASSWORD             | string        | postgres                    | database user password                                                                                                                                                                                    |
| DB_DATABASE             | string        | postgres                    | database shema name                                                                                                                                                                                       |
| AUTH_LOGIN_URL          | string        | http://localhost:3000/login | client login url as sent in login token email                                                                                                                                                             |
| AUTH_LOGIN_TOKEN_LENGTH | int           | 8                           | length of login token                                                                                                                                                                                     |
| AUTH_LOGIN_TOKEN_EXPIRY | time.Duration | 11m                         | login token expiry                                                                                                                                                                                        |
| AUTH_JWT_SECRET         | string        | random                      | jwt sign and verify key - value "random" creates random 32 char secret at startup (and automatically invalidates existing tokens on app restarts, so during dev you might want to set a fixed value here) |
| AUTH_JWT_EXPIRY         | time.Duration | 15m                         | jwt access token expiry                                                                                                                                                                                   |
| AUTH_JWT_REFRESH_EXPIRY | time.Duration | 1h                          | jwt refresh token expiry                                                                                                                                                                                  |
| EMAIL_SMTP_HOST         | string        |                             | email smtp host (if set and connection can't be established then app exits)                                                                                                                               |
| EMAIL_SMTP_PORT         | int           |                             | email smtp port                                                                                                                                                                                           |
| EMAIL_SMTP_USER         | string        |                             | email smtp username                                                                                                                                                                                       |
| EMAIL_SMTP_PASSWORD     | string        |                             | email smtp password                                                                                                                                                                                       |
| EMAIL_FROM_ADDRESS      | string        |                             | from address used in sending emails                                                                                                                                                                       |
| EMAIL_FROM_NAME         | string        |                             | from name used in sending emails                                                                                                                                                                          |
| ENABLE_CORS             | bool          | false                       | enable CORS requests                                                                                                                                                                                      |

### Testing

Package auth/pwdless contains example api tests using a mocked database.
