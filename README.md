The error you're encountering, `ERREUR: l'opérateur n'existe pas : character varying ~~ bytea`, suggests that there is a type mismatch between a string (`character varying`) and a `bytea` (binary data) field in your query.

The specific error is likely caused by one of the `LIKE` conditions in your query where you're trying to perform a pattern match (`~~` is the PostgreSQL operator for `LIKE`), but the field you're comparing (`r.someField`) is a `bytea` type, not a string. This is why PostgreSQL cannot find the correct operator for the comparison.

Here are a few things you can check:

1. **Check the Field Type**: Verify that the fields you're querying (like `r.serviceFo`, `r.reference`, etc.) are of the correct type. If they're supposed to be strings, ensure they are defined as `VARCHAR`, `TEXT`, or similar in the database, not as `bytea`.

2. **Ensure Correct Type for `LIKE` Operations**: If `r.someField` is indeed a binary field (`bytea`), you cannot use `LIKE` directly. You would need to convert the `bytea` field to a text representation before performing the comparison. This can be done using PostgreSQL's `encode` function, for example:
   
   ```sql
   AND ( :#{#criteria.someField} is null or encode(r.someField, 'escape') LIKE :#{#criteria.someField} )
   ```

3. **Check the `searchCriteria` Types**: Ensure that the `searchCriteria` properties you pass (like `r.reference`, `r.serviceFo`, etc.) match the expected types in the database. For example, if you're passing a string to `r.serviceFo`, ensure that `r.serviceFo` is a `VARCHAR` or `TEXT` field, not `bytea`.

4. **Debug and Narrow Down the Problem**: To pinpoint which specific condition is causing the issue, you can try commenting out some of the conditions one by one or checking the logs to see which exact field causes the type mismatch.

Let me know if this helps or if you need more guidance!
