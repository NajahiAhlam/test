Perfect, thanks for pasting the code \u2014 now we can be **very precise** \U0001f44d
What you\u2019re seeing actually makes total sense given this implementation.

---

## \U0001f534 What is REALLY happening

### 1\ufe0f\u20e3 Line 505 **is correctly identified**

```java
throw new ValidationException(error.getCode(), error.getMessage(), error.getField());
```

So **yes**, the exception is thrown **intentionally** from validation.

---

### 2\ufe0f\u20e3 Why the JMS log shows

```
ValidationException: null
```

This is the key confusion point \U0001f447
It **does NOT mean** `error.getMessage()` is null.

It means:

\U0001f449 **`ValidationException.getMessage()` returns `null`**

Very common when:

* `ValidationException` does **not pass the message to `super(message)`**
* Or overrides `getMessage()` incorrectly

---

## \U0001f50d Proof from your logs

You said:

> the only log shown from validate method is

```java
ITFLogger.info(this, "validate", body);
```

That tells us:

\u2705 `validate()` **is entered**
\u274c No error logs
\u274c No traceEnd log
\u27a1\ufe0f Validation **returns errors**, not an exception

Then **updateTransferFile() throws the exception**, and JMS logs it.

---

## \u26a0\ufe0f The REAL bug (100% sure)

Your `ValidationException` class is broken or incomplete.

### Typical broken implementation

Something like this \U0001f447

```java
public class ValidationException extends Exception {
    private String code;
    private String field;

    public ValidationException(String code, String message, String field) {
        this.code = code;
        this.field = field;
        // \u274c message NOT passed to super
    }
}
```

\u27a1\ufe0f Result:

```
ValidationException: null
```

---

## \u2705 THE FIX (VERY IMPORTANT)

### \u2705 Correct ValidationException constructor

```java
public class ValidationException extends Exception {

    private final String code;
    private final String field;

    public ValidationException(String code, String message, String field) {
        super(message); // \u2705 THIS IS THE FIX
        this.code = code;
        this.field = field;
    }

    public String getCode() {
        return code;
    }

    public String getField() {
        return field;
    }
}
```

After this, your log will become:

```
ValidationException: Bordereau manquant
```

instead of `null`.

---

## \U0001f9e0 Secondary issue you should also fix (hidden NPE risk)

This line is dangerous:

```java
request.getPayload().getOriginInfo().getOriginType()
```

If `originInfo` is `null`, you\u2019ll get an NPE **before validation**.

### Safer version:

```java
if (request.getPayload().getOriginInfo() != null
    && request.getPayload().getOriginInfo().getOriginType() == FileTransferOriginType.Email) {
```

---

## \u2705 Why only ONE validate log appears

Because:

* `validate()` is called once
* It returns `errors`
* You immediately throw the exception
* No further logs are executed

This is expected behavior \u2714\ufe0f

---

## \U0001f9fe TL;DR (short & clear)

* \u2705 JMS is fine
* \u2705 Validation logic works
* \u274c `ValidationException` does **not call `super(message)`**
* \u274c That\u2019s why JMS prints `ValidationException: null`
* \u2705 Fix the constructor
* \u2705 Add null-check for `originInfo`

---

If you want, next we can:

* Add **clean JMS ErrorHandler**
* Prevent **message redelivery loops**
* Standardize **business vs technical exceptions**
* Improve validation logging (very useful in prod)

Just say the word \U0001f44c
