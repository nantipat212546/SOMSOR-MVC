# SOMSOR MVC

Refactored from original SOMSOR Express app.

- View: `public/index.html`, `public/app.js`, `public/style.css`
- Controllers: `controllers/` request handlers and validation
- Models: `models/` database setup and data access; some SQL still resides in controllers and may be migrated further
- Routes: `routes/` HTTP routing
- Middleware: `middleware/auth.js`
- Config: `config/`

## Start (Windows CMD)

```cmd
set "PATH=C:\nodejs22;%PATH%"
cd /d C:\Users\Vivobook\Desktop\SOMSOR_MVC
set SEED_DEMO=true
set JWT_SECRET=somsor-local-development-secret-change-me
npm install
npm start
```

If migrating existing data, copy `somsor.db` from your existing SOMSOR folder into the new folder **while both servers are stopped**. If SQLite WAL files are present, use SQLite backup or copy the DB and WAL/SHM files consistently. Always back up before migrating.

## Important
- Original endpoints and UI preserved.
- TC-034 guard added: only pending requests can be assigned.
- Rerun all functional and API tests after this refactor.
- Not all SQL is moved into model files yet; this is a controller/route split with a model layer for key entities, not a fully strict MVC separation.
