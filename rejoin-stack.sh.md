# Use `rejoin-stack.sh` to terminate or restart OpenStack services

## `rejoin-stack.sh` could let you join stack `screen`.
### Basic operations of `screen`:
1. Navigate: 
    `ctrl + a + " (press shift + ')`
2. List screen:
    `$ screen -ls`
3. Re attach screen:
    `$ screen -r (the number you got from above cmd)`

### Terminate or restart service:
1. `$ ./rejoin-stack.sh`
2. Press `ctrl + a + '` switch between different services. You should see different numbers representing each services in the bottom of your terminal window. Input the number when you see "Switch to window:".
3. Terminate a service: `ctrl + c`
4. Restart service: press `↑` , then press enter
5. exit screen to shell: `ctrl + a + d`