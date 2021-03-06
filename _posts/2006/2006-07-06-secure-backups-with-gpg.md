---
layout: post
title: "Secure Backups with GPG"
id: 4cf5b683dabe9d2c610006f7
updated: 2010-11-30T21:44:19-05:00
date: 2006-07-06T14:26:00-04:00
tags:
- life
redirect_from: /blog/archives/2006/07/06/secure-backups-with-gpg/
---

Recently I had a client who wanted to store server backups on their hosting provider's FTP server. The only problem is that those backups contained confidential customer data. So I came up with the idea of using a public GPG key to encrypt backups before copying them to the FTP server.

A little searching revealed that a lot of other people have also used this method. A great guide for getting set up to do this can be found <a title="Notes for using GPG to encrypt and decrypt backup files" href="http://www.somacon.com/p107.php">here</a>.

I already had a GPG key pair, so all I had to do was import my public key on the server, change the trust settings, and write a few bash scripts to dump the DB, create an encrypted tar archive, and FTP it.

Here are the commands I used to encrypt the database dump and tar archive:

    # dump PostgreSQL data and encrypt it
    pg_dump dbname | gpg -r C0E3268C -e -o /tmp/backup.sql.gpg

    # tar the directories I want to backup and encrypt them
    tar -c /var /etc | gpg -r C0E3268C -e -o /tmp/backup.tar.gpg
