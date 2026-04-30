---
applyTo: "src/**/*.{tsx,scss,css}"
description: "Use when styling React components, deciding between Tailwind and SCSS, or implementing responsive behavior."
---

# Styling Rules

## Tailwind First

- Use Tailwind for layout, spacing, typography, color, and responsive structure.
- Follow the tokens and palette already defined in the project configuration.

## SCSS Exceptions

- Use SCSS only for cases where utility classes are a poor fit, such as complex animations, pseudo-element-heavy patterns, or third-party overrides.
- Keep SCSS scoped to the owning component or feature to avoid global leakage.

## Responsive Design

- Build mobile-first.
- Use responsive breakpoints intentionally instead of duplicating styles across sizes.
