# Windframe Authentication

Read this when the key is missing or access fails. Keep the original UI request and selected style/color while resolving setup.

## Check availability

Check presence only in the actual agent process. Do not enumerate environment variables or print the key.

Bash:

```bash
if [ -n "${WINDFRAME_API_KEY:-}" ]; then
  printf 'WINDFRAME_API_KEY is available\n'
else
  printf 'WINDFRAME_API_KEY is missing\n'
fi
```

PowerShell:

```powershell
if ([string]::IsNullOrWhiteSpace($env:WINDFRAME_API_KEY)) {
    'WINDFRAME_API_KEY is missing'
} else {
    'WINDFRAME_API_KEY is available'
}
```

## First use

Direct the user to sign in at [their Windframe account page](https://app.windframe.dev/account) and create a key through the account's API-key controls. If those controls are absent, direct them to Windframe support.

Have the user enter the key locally, never in chat. Do not put key literals in commands, shell history, source files, or generated application code.

### Temporary environment

Run in the user's terminal, then launch the agent from that same terminal.

Bash:

```bash
set +x
read -r -s -p 'Windframe API key: ' WINDFRAME_API_KEY
printf '\n'
export WINDFRAME_API_KEY
# Launch your agent from this terminal.
```

PowerShell 7:

```powershell
$env:WINDFRAME_API_KEY = Read-Host 'Windframe API key' -MaskInput
# Launch your agent from this terminal.
```

These commands affect the current shell and its children only. A tool subprocess cannot update its parent agent. An already running desktop agent must be restarted with the configured environment.

### Future sessions

Save the key in an existing OS secret store or password manager. Configure its environment-injection or launch integration to supply that entry as `WINDFRAME_API_KEY` each time the agent starts; unlock the store as required. Storing the secret alone does not inject it into an agent.

Ask which OS and secret manager the user uses only if exact integration commands are needed. Until configured, the hidden prompt can be used on each launch. Do not invent secret-manager commands, silently write plaintext shell-profile entries, or assume `.env` files load automatically. This skill does not save or refresh credentials.

To remove temporary access from the current shell:

```bash
unset WINDFRAME_API_KEY
```

```powershell
Remove-Item Env:WINDFRAME_API_KEY -ErrorAction SilentlyContinue
```

This does not revoke a key, remove it from a secret store, or erase copies inherited by running processes.

## Validate and resume

Recheck presence in the actual agent process after setup. Call authenticated `GET /ui-styles` to validate access and obtain live options in one request. Resume the original task without repeating the brief or valid selections.

Read [api.md](api.md) for request examples and error handling. Build headers inside the HTTP client from the environment, disable verbose tracing, and never forward credentials across redirects. Do not report raw exceptions or request headers.
