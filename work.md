## fetch
### instruction memory
read 0th byte and assign to byte_0 output

if 0th byte is:
    - 2fn
    - 30
    - 40
    - 50
    - 6fn
    - A0
    - B0
    then read next byte and assign to byte_1 output

if 0th byte is:
    - 30
    - 40
    - 50
    - 7fn
    - 80
    then read next 8 bytes and assign to bytes 2-9 output
