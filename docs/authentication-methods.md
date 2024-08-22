# Authentication methods

An authentication method is a way to verify the identity of a user trying to access the database. It defines how the server checks if the credentials provided are correct and whether the user can connect.

MySQL server and client must be configured to support `caching_sha2_password`, which can be specified when creating or modifying user accounts.

## Common Authentication Methods

| Method Name            | Description                                                                                                               |
|------------------------|---------------------------------------------------------------------------------------------------------------------------|
| `caching_sha2_password`  | This is the default authentication plugin in MySQL 8 and compatiable servers. It uses SHA-256 hashing with caching for faster authentication.       |
| `mysql_native_password`  | This method uses a native password hashing algorithm that was used in servers before version 8. It is now deprecated and may be removed in future releases. |

### Method advantages

| Authentication Method   | Advantage                  | Description                                                                                   |
|-------------------------|----------------------------|-----------------------------------------------------------------------------------------------|
| caching_sha2_password   | Better security            | Provides stronger hashing algorithms and additional security features.                       |
| caching_sha2_password   | Performance improvement    | Caches passwords to speed up authentication for repeated logins.                              |
| mysql_native_password   | Broader compatibility      | Works with a wide range of older MySQL clients and versions.                                 |
| mysql_native_password   | Simplicity                 | May be easier to use in environments where upgrading MySQL or client software is not feasible. |


## How caching_sha2_password works


| Method                | Action                         | Description                                                                                                                                           |
|-----------------------|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| Password hashing      | Hashing with SHA-256           | MySQL hashes the user’s password using SHA-256, a secure algorithm that converts the password into a fixed-size string, making it difficult to reverse-engineer. |
| Initial authentication| Username and challenge exchange| The client sends the username, the server responds with a challenge, and the client hashes the password with this challenge before sending it back.   |
| Server verification    | Compare hashed values          | The server compares the hashed value sent by the client with the stored hash. If they match, the server authenticates the user and grants access.     |
| Password caching      | Store authentication results   | `caching_sha2_password` caches the results of authentication attempts, allowing quicker logins if the user reconnects within a certain time frame.    |
| Secure connections    | Encrypt communication          | Connections using `caching_sha2_password` are encrypted, ensuring the password and authentication data are secure during transmission.                |

### How mysql_native_password works

When using the `mysql_native_password` authentication method, the authentication process is as follows:

| Action                | Description                                                                                                                                                                   |
|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Password Hashing   | MySQL hashes the password using a secure algorithm (SHA-1) when a user account is created or a password is changed. The hash is stored in the `mysql.user` table.             |
| Initial Authentication | The client sends the username to the MySQL server. The server generates a random string called a nonce (challenge) and sends it back to the client.                  |
| Client Response    | The client combines the hashed password with the nonce, hashes this combination, and sends the resulting value back to the server.                                           |
| Server Verification| The server hashes the stored password with the same nonce, compares this hash with the value sent by the client, and if they match, authenticates the user and grants access. |

Overall, the `mysql_native_password` method ensures that even if someone intercepts the data being transmitted, they cannot easily reverse-engineer the original password due to the hashing process and the use of a unique nonce for each login attempt.

