# Cryptography-5

This repository includes a simple server application showcasing secure user registration and login. Flask web framework is used for creating the server, serving the login page, and processing the requests. The registration/login routines are implemented manually.

## Password storage

Instead of storing the passwords directly, they're hashed using SHA-512 and Argon2, and only after that inserted into the `credentials` table of the sample database. SHA-512 is used first to "normalize" the password regardless of how long it is. Then the previously-calculated hash is passed as the input to Argon2 working in `id` mode, which is both cryptographically secure, and resistant to GPU cracking attacks due to high memory requirements and long processing time.

## Registration

When the user tries to register, the password is immediately checked to have a minimum length of 16 characters which should be secure enough for non-sensitive data at the time of this writing. If that is not true, the registration immediately fails, and the user is informed about it. Otherwise, the routine continues, and the password is hashed regardless of the fact whether another user with the same email already exists to prevent timing attacks. If the email address is unique, a new record is added to the database. In the end, a generic response is sent to the user saying that an activation link was sent to the provided email address - this way a potential attacker cannot learn whether a new user was registered or not.

## Login

When the user tries to log in, a database record is fetched based on the provided email address, and it is verified that the stored Argon2 hash was computed from the SHA-512 hash of the provided password. If no record with the provided email address exists in the database, or the password verification fails, the user receives an error message about invalid credentials with no indication which step of the routine resulted in failure.
