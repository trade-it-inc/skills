# Trade It Skill

This repository contains the `trade-it` skill for use with skills.sh and compatible AI agent runtimes.

## Contents

- `SKILL.md`: primary skill definition and workflow
- `agents/openai.yaml`: UI metadata for compatible skill catalogs
- `references/api-contract.md`: request/response contract and integration reference
- `references/trade-config.md`: trade config fields, enums, and examples

## Skill

- Name: `trade-it`
- Invocation: `$trade-it`

## Purpose

Use this skill to help an AI one-shot implement the Trade It embedded SDK in a partner application, including:

- server-side session URL proxy routes
- client-side `@trade-it/react` modal wiring
- connect and trade launch flows
- correct Trade It request/response handling
