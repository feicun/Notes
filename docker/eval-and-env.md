1. eval: eval [arg ...]
    ```
    Execute arguments as a shell command.

    Combine ARGs into a single string, use the result as input to the shell,
    and execute the resulting commands.

    Exit Status:
    Returns exit status of command or success if command is null.
    ```

2. Unset from current docker env(back to host's bash):
`$ eval "$(docker-machine env -u)"`

3. Check env:
`$ env | grep DOCKER`
