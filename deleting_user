# Deleting a Keycloak User via PostgreSQL

This guide provides step-by-step instructions to delete a Keycloak user directly from the PostgreSQL database.

```
# Access the PostgreSQL Database:
sudo -u postgres psql

# Find the User ID:
SELECT id, username FROM user_entity WHERE username = '<admin-username>';

# Delete Related Data:
# Replace <user-id> with the retrieved ID from the previous step.

DELETE FROM credential WHERE user_id = '<user-id>';
DELETE FROM user_role_mapping WHERE user_id = '<user-id>';
DELETE FROM user_attribute WHERE user_id = '<user-id>';
DELETE FROM user_required_action WHERE user_id = '<user-id>';
DELETE FROM federated_identity WHERE user_id = '<user-id>';

# Delete the User:
DELETE FROM user_entity WHERE id = '<user-id>';

# Exit PostgreSQL:
\q

# Restart Keycloak:
sudo systemctl restart keycloak
```

## Example:
If the username is `admin`, and the ID retrieved is `c16df2c0-ec30-4464-b0e2-146b5ecaf214`, run the following commands:

```
sudo -u postgres psql
SELECT id, username FROM user_entity WHERE username = 'admin';

DELETE FROM credential WHERE user_id = 'c16df2c0-ec30-4464-b0e2-146b5ecaf214';
DELETE FROM user_role_mapping WHERE user_id = 'c16df2c0-ec30-4464-b0e2-146b5ecaf214';
DELETE FROM user_attribute WHERE user_id = 'c16df2c0-ec30-4464-b0e2-146b5ecaf214';
DELETE FROM user_required_action WHERE user_id = 'c16df2c0-ec30-4464-b0e2-146b5ecaf214';
DELETE FROM federated_identity WHERE user_id = 'c16df2c0-ec30-4464-b0e2-146b5ecaf214';
DELETE FROM user_entity WHERE id = 'c16df2c0-ec30-4464-b0e2-146b5ecaf214';

\q
sudo systemctl restart keycloak
```

## Important Notes:
- **Backup First**: Always create a backup of your database before making direct changes.
- **Foreign Key Constraints**: Ensure you delete dependent records from related tables to avoid foreign key violations.
- **Restart Keycloak**: Restart the Keycloak server to apply the changes using `sudo systemctl restart keycloak`.

## Troubleshooting:
- If you encounter errors related to foreign key constraints, ensure you delete data in the correct order as specified in this guide.
- Verify the user's `id` and related records are fully removed by running:
  ```
  SELECT * FROM user_entity WHERE id = '<user-id>';
  ```
