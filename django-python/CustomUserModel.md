# Using a Custom User model in django
check the docs for setup : `https://docs.djangoproject.com/en/5.2/topics/auth/customizing/#django.contrib.auth.models.BaseUserManager`

---

## 1️⃣ What are they?

### AbstractUser

* Includes:

  * `username`, `first_name`, `last_name`, `email`, `is_staff`, `is_active`, `is_superuser`, `last_login`, `date_joined`.
  * Django’s full authentication and admin compatibility out of the box.
  * You can remove unused fields, but they exist unless explicitly overridden.

---

### AbstractBaseUser

* Includes:

  * `password`, `last_login`, and nothing else.

* You must define:
  * Your own `USERNAME_FIELD`.
  * Required fields.
  * A custom `UserManager` with `create_user`, `create_superuser`.
* Gives full control over fields and behavior.

---

## 2️⃣ When to use each?

✅ Use `AbstractUser` if:

* You are fine with Django’s existing fields (like `username`, `first_name`, `last_name`) staying in your table.
* You want easy admin integration with minimal configuration.
* You want to move quickly and don’t need custom identity structures.

✅ Use `AbstractBaseUser` if:

* You want full control over fields (e.g., email as the only identifier, UUID PK, no `username`).
* You want a truly lean User table without unnecessary fields.
* You are building a clean, modern authentication system (e.g., JWT + OTP + OAuth) with email-first or email-only login.

---

