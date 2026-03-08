# Development

## Local checks

Install the PHP development tools:

```bash
composer install
```

Install the git hooks for this repository:

```bash
./install-git-hooks.sh
```

The pre-commit hook will:

- Run `php -l` on staged PHP files
- Run `vendor/bin/phpcs --standard=phpcs.xml.dist` when PHPCS is installed

## GitHub Actions

- `🧪 CI` runs PHP syntax checks, PHPCS quality checks, and packaging
- `🔐 Dependency Review` reviews dependency changes in pull requests

## Security note

GitHub CodeQL currently does not support PHP. For this repository, PHP security coverage comes from:

- `WordPress.Security` sniffs in PHPCS
- `PHPCompatibilityWP` checks for compatibility-related risk
- dependency review in pull requests

## Standards

PHPCS uses:

- `WordPress-Core`
- `WordPress-Docs`
- `WordPress-Extra`
- `WordPress.Security`
- `PHPCompatibilityWP`
