---
name: Template Inheritance
---

A Jinja2 feature where a child template (`{% extends "base.html" %}`) inherits the structure of a parent template. The parent defines the shared layout (nav, footer, CSS) with `{% block %}` placeholders. The child fills in only the parts that change. This follows the DRY principle — shared structure lives in one file.
