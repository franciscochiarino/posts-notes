---
title: "Ngrok and json-server"
date: 2023-08-13T15:00:00+02:00
summary: Setting up a local server and creating a tunnel with Ngrok.
draft: false
tags: [json-server, ngrok]
---

## Installation

Run this code to create a directory and initialize npm:
```
mkdir jsonserver && jsonserver && npm init
```

Once you've done that, install json-server and ngrok:
```
npm install json-server ngrok
```

## Setup

Go open the package.json file in the jsonserver directory and add these scripts:
```
  ...
  "scripts": {
    "db": "json-server -w db.json",
    "tunnel": "ngrok http 3000"
  },
  ...
```

Within your project terminal, run `npm run db` to start your server and `npm run tunnel`, which will create a tunnel in port 3000 where you can access your server. By default `json-server` runs on port 3000, this is why we set the tunnel to be in port 3000 as well.

### Why don't we just access json-server directly?

We can do that from our machine, but not from our phone. This is the hole point of creating the tunnel with Ngrok, so we can access our server inside our computer from anywhere.

When you run `npm run tunnel` (which is just an "alias" for `ngrok http 3000`) you'll see something like this in your terminal:
```
Session Status                online
Account                       Francisco (Plan: Free)
Version                       3.3.5
Region                        Europe (eu)
Latency                       -
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://c238-81-203-49-213.ngrok-free.app -> http://localhost:3000

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```
If you have a look at "Forwarding" it contains an address that forwards you to `localhost:3000`. We can use this address to make requests from our phone.
