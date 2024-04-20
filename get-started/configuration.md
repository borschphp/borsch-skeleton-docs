# Configuration

## Public directory

After installing Borsch Skeleton, configure your web server's root to be the `public` directory.  
The `index.php` in the entry point for all HTTP requests of your application.

## Environment file

When installed via Composer `create-project` command line, a `.env` file is automatically generated in the root folder
of your application.

If you did not use `create-project` command line then copy the `.env.example` file and rename it as `.env`.  
Typically: `cp .env.example .env`.

Review the default environment variable, update them, add your own, **add a proper `APP_KEY` if not done yet during the
`create-project` command**.

## Permissions

Make sure directories within `storage` directory are writable by your web server or it will not run.