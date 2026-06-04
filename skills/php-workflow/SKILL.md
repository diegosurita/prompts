---
name: php-workflow
description: Workflow for PHP tasks that enforces PSR conventions, uses Context7 for PHP-version-specific guidance when available, and checks for security and data leakage risks.
---

# PHP Workflow

Use this skill for any PHP task.

## Instructions

1. Identify the project's PHP version from `composer.json`, CI config, or docs.
2. Use Context7 documentation when available to confirm version-specific PHP features and library APIs. If Context7 is unavailable, fall back to the project's own docs and code. You can also check PHP official documentation for version-specific features.
3. Follow PSR conventions, especially PSR-12 and PSR-4, and match existing repository patterns.
4. Review changes for security and data leakage risks before finishing.
5. Prefer the smallest safe change that preserves existing behavior.

## Security checklist

- Avoid exposing secrets, tokens, credentials, stack traces, or internal paths.
- Validate and sanitize untrusted input before use.
- Review authorization, authentication, and access-control paths.
- Avoid debug dumps or logging sensitive data.
