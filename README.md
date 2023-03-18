# Dockerized IMAP server#

IMAP server for debugging.

**IMPORTANT**: This image is **ONLY** for developing/debugging proposes

This docker image is based on https://github.com/tomav/docker-mailserver and includes only Postfix (SMTP) and Dovecot (IMAP) servers with one catchall mailbox `debug@example.com` for all emails. So, it's very useful for debugging. Optionally, you can define another normal mailbox.

Every email received via SMTP will be delivered locally to `debug@example.org`, so it's safe for testing a web application sending emails with a production list of emails.

Using your favorite email client, you can connect via the IMAP protocol to see emails like the original recipient would have received them.

## Run container with docker compose

Edit ```docker-compose.yml``` and potentially change these environment variables:

- MAILNAME: Mail domain (by default, `example.com`)
- MAIL_ADDRESS: Normal user mailbox email address (optional)
- MAIL_PASS: Normal user mailbox password

then do 

```bash
docker-compose build
docker-compose create
```

This will create the imap container but not yet start it. Before you can use the Server, you need to copy certificates to the container, for example:

```bash
docker cp /location/to/your.crt imap:/etc/ssl/certs/ssl-cert-imap.pem
docker cp /location/to/your.key imap:/etc/ssl/private/ssl-cert-imap.key
```

Now you can start the container

```bash
# bring up the mail server
docker compose up -d
```

In order to have postfix (SMTP) in the container use the same certificates do the following:

```bash
# just fix the certificate location for postfix
docker exec imap postconf -e smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-imap.pem
docker exec imap postconf -e smtpd_tls_key_file=/etc/ssl/private/ssl-cert-imap.key 
docker exec imap systemctl reload postfix
```

Configure your email client with these parameters and test it sending any email to any email address 

### Catch all debug mailbox

- **IMAP server:** `imap`
- **IMAP encryption:** `SSL`
- **IMAP port:** `993`
- **IMAP username:** `debug@example.com`
- **IMAP password:** `debug`

- **SMTP server:** `imap`
- **SMTP encryption:** `No`
- **SMTP port:** `25`
- **SMTP authentication:** `none`

### Normal user mailbox (Optional)

- **IMAP server:** `imap`
- **IMAP encryption:** `SSL`
- **IMAP port:** `993`
- **IMAP username:** `address@example.org` (change `address@example.org` to your `MAIL_ADDRESS`)
- **IMAP password:** `pass` (change `pass` to your `MAIL_PASS`)

- **SMTP server:** `imap`
- **SMTP encryption:** `No`
- **SMTP port:** `25`
- **SMTP authentication:** `none`
