# Frequently Asked Questions

## 1. How can I request a cursor license?
To request a cursor license, go to the service desk and open a ticket.
Fill it in with your email, user name, and squad.

--------

## 2. Can't connect to the office's wifi
  If you are not able to connect to the office's wifi, assure that your certificate still valid. 
  On the terminal run:
    ```sh
    echo | openssl s_client -connect google.com:443 -servername google.com 2>/dev/null | openssl x509 -noout -dates -subject -issuer
    ```
  If the certificate has expired, renew it and try connecting again.
